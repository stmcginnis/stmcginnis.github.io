---
title: Stein Release Goal Selection
categories:
  - OpenStack
tags:
  - "trip report"
---

Here's my attempt to recap the goal selection discussion we had last week at
the Forum. Feel free to correct any misstatements and continue the discussion.

For reference, here's the etherpad from the discussion:

[https://etherpad.openstack.org/p/YVR-S-release-goals](https://etherpad.openstack.org/p/YVR-S-release-goals)

### Overall Goal Discussion

We started off by discussing the reason for having the release cycle goals and
what we should actually be trying to achieve with them. There were some who
expressed concerns about the current Rocky goal selections not being the right
things we should be focusing on. The hope with the first part was to come to
some sort of agreement, or at least common understanding, that would inform our
selections for Stein.

Some thought the goals should be entirely operator facing. So things that have
an obvious and direct improvement for operators. At least so far, our goal
selection has mostly been to try to get one goal that is a visible thing like
that while the other is more of a technical debt cleanup. The idea being that
the tech debt ones will keep us in a good and healthy position to be able to
continue to address operator and user needs more easily.

There was also a desire to make the goals more "fully baked" before making them
a goal. This would mean having the necessary changes well documented with
example patches for teams to refer to to help guide them in figuring out what
needs to be done in their own repos.

There was also the desire to make these goals something that can generate some
excitement and be things that can be more of a marketing message. Things like
"OpenStack services now support live configuration changes" vs. "OpenStack got
rid of a testing library that no one has heard of".

And I almost missed it, but there was a great suggestion to have a #goals
channel for folks to go to for help and to discuss goal implementation. I
really like this idea and will bring it up in the next TC office hour to see if
we can get something official set up.

### Stein Goals

We ended up with only 10-15 minutes to actually discuss some ideas for goal
selection for Stein. This was expected and planned. It will take some further
discussion and thought before I think we are ready to actually pick some goals.

Some of the more popular ones brought up in the session were:

- Cold upgrade support
- Python 3 first
- Addition of "upgrade check" CLIs

We were also able to already identify some possible goals for the T cycle:

- Move all CLIs to python-openstackclient
- Adopt a larger set of default roles

We've been collecting a "goal backlog" with these and other ideas here:

[https://etherpad.openstack.org/p/community-goals](https://etherpad.openstack.org/p/community-goals)

All ideas (and support implementing them!) are welcome. Please join the
discussion on the openstack-dev mailing list if you would like to get involved.
