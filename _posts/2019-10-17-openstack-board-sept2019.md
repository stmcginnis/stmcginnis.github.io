---
title: September 2019 OpenStack Board Notes
categories:
  - OpenStack
tags:
  - "OSF Board"
---

There was another OpenStack Foundatoin Board of Directors conference call on
September 10, 2019. There were a couple of significant updates during this
call. Well, at least one significant for the community, and one that was
significant to me (more details below).

In case this is your first time reading my BoD updates, just a reminder that
upcoming and past OSF board meeting information is
[published on the
wiki](https://wiki.openstack.org/wiki/Governance/Foundation#OpenStack_Board_of_Director_Meetings)
and the meetings are open to everyone. Occasionally there is a need to have a
private, board member only portion of the call to go over any legal affairs
that can't be discussed publicly, but that should be a rare occasion.

## September 10, 2019 OpenStack Foundation Board Meeting

The original [agenda can be found
here](https://wiki.openstack.org/wiki/Governance/Foundation/10September2019BoardMeeting).
Usually there are official and unofficial notes sent out, but at least at this
time, it doesn't appear Jonathan has been able to get to that. Watch for that
to show up on the wiki page referenced in the previous section.

### Director Changes

There were a couple of changes in the assigned Platinum Director seats. The
Platinum level sponsors are the only seats on the board that are guaranteed to
the sponsor and allows them to assign a Director. So no change in sponsorships
at this point, just a couple of internal personel changes that led to these
changes.

With all the churn and resulting separation of Futurewei in the US from the
rest of Huawei, their chair seat was moved over to Fred Li. I worked with Fred
quite a bit during my time with the company. He's a great guy and has put in a
lot of work, mostly behind the scenes, to support OpenStack. Really happy to be
able to work with him again. Anni has also done a lot over the years, so sad to
see her go. I'm sure she will be quite busy on new things though.

On the Red Hat side, Mark McLoughlin has transitioned out, handing things over
to Daniel Becker. It sounds like with the internal structure at Red Hat, Daniel
is now the better representative for the OpenStack Foundation. I personally
didn't get a lot of opportunity to work with Mark, but I know he has been
around for a long time and has done a lot of great things, so I'm a little sad
to see him go. But also looking forward to working with Daniel.

### Director Diversity Waiver

This was the significant topic to me, because, well... it was about me.

In June I switched employers, going back to Dell EMC. So far, I've been very
happy, and it feels like I've gone bake home with the 14+ years between
Compellent and Dell that I had prior to joining Huawei. Not that my time with
Huawei wasn't great. I think I learned a lot and had some opportunities to do
things that I hadn't done before, so no regrets.

But the catch with my going back to Dell was that they already have a Gold
sponsor seat with Arkady Kanevsky and a community spot with Prakash
Ramchandran.

The [OpenStack Foundation
Bylaws](https://wiki.openstack.org/wiki/Governance/Foundation/Bylaws) have a
section (4.17) on Director Diversity. This clause limits the number of
directors that can be affiliated with the same corporate entity to two. So even
though Prakash and I are Individual Members (which means we are there as
representatives of the community, not as representatives of our company), my
move to Dell now violated that clause.

I think this was added to the bylaws back in the days where there were a few
large corporate sponsors that had large teams of people dedicated to working on
OpenStack. It was a safeguard to ensure no one company could overrun the
Foundation based solely on their sheer number of people involved. That's not
quite as big of an issue today, but I do still think it makes sense. It is a
very good thing to make sure any group like this has a diversity of people and
viewpoints.

The bylaws actually explicitly state what should happen in my situation too -
Article 4.17(d) states:

> If a director who is an individual becomes Affiliated
> during his or her term and such Affiliation violates the Director Diversity
> Requirement, such individual shall resign as a director.

As such, I really should have stepped down on moving to Dell.

But luckily for me, there is also a provision called out in 4.17(e):

> A violation of the Director Diversity Requirement may be waived by a vote of
> two thirds of the Board of Directors (not including the directors who are
> Affiliated)

This meant that 2/3 of the Board members present, not including any of us from
Dell, would have to vote in favor of allowing me to continue out my term. If
less than that were in favor, then I would need to step down. And presumably
there would just be an open board seat for the rest of the term until elections
are held again.

There was brief discussion, but I was very happy that everyone present did vote
in favor of allowing me to continue out my term. I kind of feel like I should
have stepped out during this portion of the call to make sure no one felt
pressure by not wanting to say no in my presence, but hopefully that wasn't the
case for anyone. It was really nice get these votes, and some really good back
channel support from non-board attendees listening in on the call.

What can I say - compliments and positive reinforcement go far with me. :)

So I'm happy to say I will at least be able to finish out my term for the rest
of 2019. I will have to see about 2020. I don't believe Arkady nor Prakash are
planning on going anywhere, so we may need to have some internal discussions
about the next election. Or, probably better, leave it up to the community to
decide who they would like representing them for the Individual Director seats.
Prakash has been doing a lot of great work for the India community, so if it
came down to it and I lost to him, I would be just fine with that.

### OW2 Associate Membership

Thierry then presented a proposal to join [OW2](https://www.ow2.org/) as an
Assocaite Member. OW2 is "an independent, global, open-source software
community". So what does that mean? Basically, like the Open Source Initiative
and others, they are a group of like-minded individuals, companies, and
foundations that work together to support and further open source.

We (OpenStack) have actually worked with them for some time, but we had never
officially joined as an Associate Member. There is no fee to join at this
level, and it is really just formalizing that we are supportive of OW2's
efforts and willing to work with them and the members to help support their
goals.

They have been in support of OpenStack and open infrastructure for years, so it
was great to approve this effort. We are now listed as one of their [Associate
Members](https://www.ow2.org/view/Membership_Joining/Associate_Organizations).

### Interop WG Guidelines 2019.06

Egle moved to have the board approve the [2019.06
guidelines](https://review.opendev.org/#/c/663154). We had held an email vote
for this approval, but since we did not get reponses from every Directory, we
now performed a vote in-meeting to record the voting. All present were in
favor.

The interop guidelines are a way to make sure all OpenStack deployments conform
to a base set of requirements. This makes sure that an end user of an OpenStack
cloud has at least some level of assurance that they can move from one cloud to
another and not getting a wildly different user experience. The work of the
Interop Working Group has been very important to ensuring this stability and
helping the ecosystem around OpenStack grow.

### Miscellaneous

Prakash gave a quick update on the meetups and mini-Summits being organized in
India. Sounds like a lot of really good activity happening in various regions.
It's great to see this community being supported and growing.

Alan also made a call for volunteers for the Finance and Membership committees.
I had tried to get involved earlier in the year, but I think due to timing
there really just wasn't much going on at the time. With the next election
coming up, and some changes in sponsors, now is actually a good time for the
Membership Committee to have some more attention. I've joined Rob Esker to help
review any new Platinum and Gold memberships. Sounds like we will have at least
one new one of those coming up soon.

### Summit Events

It wasn't really an agenda topic for this Board Meeting, but I do think it's
worth pointing out here that the proposed changes to the structure of our
yearly events have gone through and 2020 will start to diverge from the typical
pattern we have had so far of holding to major Summits per year.

Erin Disney [sent out a
post](http://lists.openstack.org/pipermail/foundation/2019-September/002794.html)
about these changes to the mailing list. We will have a smaller event focused
on collaboration in the spring, then a larger Summit (or Summit-like) event in
later in the year.

With the maturity of OpenStack and where we are today, I really think this
makes a lot more sense. There simply isn't enough big new functionality and
news coming out of the community today to justify two large marketing focused
events like the Summit per year. What we really need now is to foster the
environment to make sure the developers, operators, and others that are working
on implementing new functionality and fixing bugs have the time and venue they
need to work together and get things done. Having these smaller events and
supporting more things like the regional Open Infrastructure Days will
hopefully help keep that collaboration going and allow us to focus on the
things that we need to do.

And the next event will be in beautiful Vancouver again, so that's a plus!
