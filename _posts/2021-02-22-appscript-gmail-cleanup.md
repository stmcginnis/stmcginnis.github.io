---
title: Cleaning up Gmail with App Script
categories:
  - uncategorized
tags:
  - Gmail
  - "Apps Script"
---

After several years working in the open source community, I've ended up with a
lot of emails from Gerrit code reviews, Google Group mailing lists, and various
GitHub notifications. And when I say a lot, I mean my Google storage for
Gmail, Photos, Drive, and others were getting closer and closer (78%) to the
max free capacity.

To be fair, I do know quite a bit of that is from other things besides email.
I have several large files in Drive, and a few longer videos from when my kids
were younger sitting in Google Photos. But the weird thing that struck me was
that even though I would delete emails from things like code reviews, they would
still show up when I searched by inbox for various terms. I *thought* the way
it was supposed to work was that I would delete an email, it would sit in my
Trash for 30 days or so, then Gmail would clean things up for me. Based on what
I was getting from my inbox searches, that apparently isn't always the case.

So rather than allow things to keep up slowly creeping up to max out my storage
quota, I decided to take a look at what I could do to clear out this old cruft.
At first I would perform some searches, use the Select All to select all 50
messages on the page, and delete them. And the next page. And the next.

That was going to take a long time. It wouldn't give me the message count from
queries like `from:review@openstack.org`, but it looked like it was in the
1000's. Even with no where to go, I didn't have time to click through all that.

## Automation (sorta) to the rescue

I'm probably late to the party - based on forum results, it appears several
years late to the party - but it didn't take me too long to stumble on the
[Google Apps Script](https://script.google.com/home) site.

I started by looking to see if there was an API for interacting with Gmail.
Turns out there is, for several different languages. But the [Quickstart
Guide](https://developers.google.com/gmail/api/quickstart/apps-script) included
a link to how to get started with Apps Script. That has a nice walkthrough of
getting a simple script set up to interact with Gmail.

From there, it was pretty easy to adapt the example they gave to work with
messages instead. Looking through the API documentation, I found the message
object has a `moveToTrash()` method. That led me to come up with this quick
example:

```javascript
function deleteMessages() {
  var count = 100
  while (count > 0) {
    var threads = GmailApp.search('from:review@openstack.org', 0, 10)
    count = threads.length
    for (var i = 0; i < count; i++) {
      var thread = threads[i];
      for (j = 0; j < thread.getMessageCount(); j++) {
        var message = threads.getMessages()[j];
        message.moveToTrash()
      }
    }
  }
}
```

It's fairly easy to work with the Gmail API. To break it down bit, here are some
key parts.

### Getting threads

```javascript
var threads = GmailApp.search('from:review@openstack.org', 0, 10)
```

This queries the Gmail API, using a query just like you would use within the
Gmail UI itself. In fact, I highly recommend first running your query in the UI
and verifying it returns what you expect it to, then copying and pasting that
query string into the script. We are deleting messages here, so you probably
should be very cautious about what gets automatically deleted.

The two numbers, `0, 10`, are the starting count and the max number of threads
to retrieve. Starting off, this query would return a very, very large amount
of messages. Pulling that all across the network and into memory would not be
very nice. So chunking this out into smaller amounts is a lot more efficient.
I would rather have to run the script repeatedly than try to load my entire
inbox into the script.

And speaking of running repeatedly, there apparently is a hard coded 5 minute
timeout on script execution. So although this speeds things up quite a bit over
manually deleting, it still does take time. So one way or another I was going
to have to keep rerunning the script to get everything cleared out anyway.

### Bringing out the trash

```javascript
message.moveToTrash()
```

Most of the rest of the code is just looping through results. The next code of
interest is the actual deletion. This is accomplished by calling the
`moveToTrash` method on the message object. Just as it sounds like, it moves
the message from wherever it is into your Trash folder.

## Slight optimization

This was great progress. I ran it a few times, and I could see from my search
result that things were getting cleared out. It was taking a while to execute,
so I looked through the API docs some more. It turns out, I was wasting some
time iterating through all those messages within the thread.

```javascript
var threads = GmailApp.search('from:review@openstack.org', 0, 10)
count = threads.length
for (var i = 0; i < count; i++) {
    var thread = threads[i];
    for (j = 0; j < thread.getMessageCount(); j++) {
        var message = threads.getMessages()[j];
```

That's looping through each thread that is returned from the query, and in
turn, retrieving and looping through each message within the thread. For
things like OpenStack code reviews, some patches with a lot of feedback,
updates, and CI comments, that can mean quite a few messages per thread.

Luckily, the thread object itself also has a `moveToTrash` method. So
knowing that, I was able to clean that up quite a bit:

```javascript
var threads = GmailApp.search('from:review@openstack.org', 0, 10)
count = threads.length
for (var i = 0; i < count; i++) {
    thread.moveToTrash()
```

This was definitely more efficient.

## Space, the final frontier

I still had a problem with the script though. Or maybe, more of a problem
with my patience and my trust in the Trash actually being emptied after
30 days. What I really wanted was to immediately and completely delete
these messages and get them out of there.

None of the examples I read in the official docs mentioned it, though
that could very well just be my impatience and not wanting to read up
on more than I really needed to. But I found an old forum posting
somewhere that mentioned a `remove` call.

**DANGER: Only do this if you are very careful!**

Seriously, don't blame me if you mess things up. I warned ya!

This call will actually do exactly what I wanted. It gets the message
completely out of there right away. Of course, with scripting and
automation, there is no "Are you really sure?" dialogs to click and
slow you down. If you get the query wrong, too late, it's gone.

So again being very cautious about the query string being used to
retrieve the threads, I tried one more approach.

## Stepping back

Now the only bad thing here (OK, probably not the *only* bad thing)
is that the remove call only works on message IDs. Not threads.

I just had to roll back some of the optimization I had done before
and go back to iterating through the messages in the thread again.
It slowed things down, but ultimately it was still faster, at least
to me, because it would permanently get rid of the messages. The
final version I ended up with was then:

```javascript
var threads = GmailApp.search('from:review@openstack.org', 0, 10)
count = threads.length
for (var i = 0; i < count; i++) {
    var thread = threads[i];
    for (j = 0; j < thread.getMessageCount(); j++) {
        var message = threads.getMessages()[j];
        Gmail.Users.Messages.remove('me', message.getId());
```

That last line takes a user ID, but luckily it has a special value
of `me` that will recognize the current user. And the ID of the
message to remove, retrieved by calling the `getId()` method on
the message. After running this version over and over after the 5
minute timeouts, I started to see my storage usage number start to
drop down.

## More cleanup

One more time - it's very important to make sure you query to get
threads is correct.

The query is very flexible though. So I was able to use wildcards
and change the to/from to also clean up noreply.github.com,
googlegroups.com, and several others where I have accumulated
mass amounts of junk over time.

As I said, I'm also impatient, so once I had these cleaned up,
I made another pass through some of the worse offenders and added
`in:trash` to the query to immediately clean out some messages
from the trash.

## All tidied up

In the end, my total Google account storage went down from
about 12.78G to 11.8G. Not bad.

I'm guessing I can't automate Google Photos clean up as well.
At least not without learning some image recognition APIs and
adding a lot of risk. So that's a clean up for another day.

But with a few quick hacky scripts, I'm pretty happy with how
much space I was able to free up.
