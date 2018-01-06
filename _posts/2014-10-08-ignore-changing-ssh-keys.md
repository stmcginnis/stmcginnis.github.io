---
title: Ignore Changing SSH Keys
---

{{ page.title }}
================

<p class="meta">8 Oct 2014</p>

In my lab I have a set of virtual machines that are automatically spun up for
various tests. This setup is mostly hands off, but occasionally I do need to
log in to one of these instances to check on something.

Since the instances are constantly changing, I was getting annoyed always
getting the following prompt:

```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@ WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED! @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ECDSA key sent by the remote host is
af:92:a4:9c:ff:bf:0b:88:2a:62:90:c3:3e:2a:3f:74.
Please contact your system administrator.
Add correct host key in /home/smcginnis/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in /home/smcginnis/.ssh/known_hosts:11
remove with: ssh-keygen -f "/home/smcginnis/.ssh/known_hosts" -R 10.125.202.186
ECDSA host key for 10.125.202.186 has changed and you have requested strict checking.
Host key verification failed.
```

I would then have to run the ssh-keygen -f command to remove the entry, then
reconnect and accept the new host key.

It turns out there is a way to make it much simpler in this scenario. If you
have a range of addresses that you know are going to be changing, you can
configure SSH to just ignore these host key changes and connect. There are
options on the command line to do this per instance, but less typing is better
in my book.

The solution is to add the following to your ~/.ssh/config file:

```
Host 10.125.202.*
UserKnownHostsFile /dev/null
StrictHostKeyChecking no
```

You can do an entire subnet as above, or you can specify individual hosts. More
options can be found in the
[ssh_config man page](http://linux.die.net/man/5/ssh_config).
