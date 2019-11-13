---
title: November 2019 OpenStack Board Notes
categories:
  - OpenStack
tags:
  - "OSF Board"
---

The Open Infrastructure Summit was held in mainland China for the first time
the week of November 4th, 2019, in Shanghai. As usual, we took advantage of the
opportunity of having so many members in one place by having a Board of
Directors meeting on Sunday, November 3.

Attendance was a little lighter due to visa challenges, travel budgets, and
other issues. But we still had a quorum with a lot of folks in the room, and
I'm sure it was a nice change for our Chinese board members and others from the
APAC region.

The original meeting agenda is [published on the
wiki](https://wiki.openstack.org/wiki/Governance/Foundation/3November2019BoardMeeting)
as usual.

### OSF Updates

Following the usual pattern, Jonathan Bryce kicked things off with an update of
Foundation and project activity.

One interesting thing that really stood out to me, which Jonathan also shared
the next day in the opening keynotes, as an analyst report putting OpenStack's
market at $7.7 billion in 2020. I am waiting for those slides to be published,
but I think this really showed that despite the decrease in investment by
companies in the development of OpenStack, its adoption and growth is stable
and growing.

This was especially highlighted in China, with companies like China UnionPay,
China Mobile, and other large companies from other industries increasing their
use of OpenStack. And public clouds like Huawei and other local service
providers basing their services on top of OpenStack.

I can definitely state from experience after that week, access to the typical
big 3 public cloud providers in the US is a challenge through the Great
Firewall. Being able to base your services on top of a borderless open source
option like OpenStack is a great option with the current political pressures. A
community-based solution, rather than a foreign tech company's offerings,
probably makes a lot of sense and is helping drive this adoption.

Of course, telecom adoption is still growing as well. I'm not as involved in
that space, but it really seems like OpenStack is becoming the de facto
standard for having a programmable infrastructure to base dynamic NFV solutions
on top of, but directly with VMs and baremetal, and as a locally controlled
platform to serve as the underlying infrastructure for Kubernetes.

### Updates and Community Reports

#### StarlingX Progress Report

The StarlingX project has made a lot of progress over the last several months.
They are getting closer and closer to the latest OpenStack code. They have been
actively working on getting their custom changes merged upstream so they do not
need to continue maintaining a fork. So far, they have been able to get a lot
of changes in to various projects. They hope to eventually be able to just
deploy standard OpenStack services configured to meet their needs, focusing
instead on the services on top of OpenStack that make StarlingX attractive and
a great solution for edge infrastructure.

#### Indian Community Update

Prakash Ramchandran gave an update on the various meetups and events being
organized across India. This is a large market for OpenStack. Recently approved
government initiatives could make this an ideal time to help nurture the Indian
OpenStack community.

I'm glad to see all of the activity that Prakash has been helping support
there. This is another region where I expect to see a lot of growth in
OpenStack adoption.

#### Interop Working Group

Egle gave an update of the Interop WG activity and the second 2019 set of
changes were approved. Nothing too exciting there, with just minor updates to
the interop requirements.

The larger discussion was about the need and the health of the Interop WG.
Chris Hoge was a very active contributor to this, but he recently left the OSF,
and the OpenStack community, to pursue a different opportunty. Egle Sigler is
really the only one left on the team, and she has shared that she would not be
able to do much more with the group other than keeping the lights on.

This team is responsible for the guidelines that must be followed for someone
to certify their service or distribution of OpenStack meets the minimum
functionality requirements to be consistent with other OpenStack deployments.
This is certification is needed to be able to use the OpenStack logo and be
called "OpenStack Powered".

I think there was pretty unanimous agreement that this kind of thing is still
very important. Users need to be able to have a consistent user experience when
moving between OpenStack-based clouds. Inconsistency would lead to unexpected
behaviors or responses and a poor user experience.

For now it is a call for help and to raise awareness. It did make me think
about how we've been able to decentralize some efforts within the community,
like moving documentation into each teams repos rather than having a
centralized docs team and docs repo. I wonder if we can put some of this work
on the teams themselves to mark certain API calls as "core", then some testing
in place to ensure none of these set APIs are changed or start producing
different results. Something to think about at least.

#### First Contact SIG Update

The First Contact SIG works on things to make getting involved in the community
easier. They've done a lot of work in the past on training and contributor
documentation. They've recently added a [Contributing Organization
Guide](https://docs.openstack.org/contributors/organizations/index.html) that
is targeted at the organization management level to help them understand how
they can make an impact and help their employees to be involved and productive.

That's an issue we've had to varying degrees in the past. Companies have had
good intentions of getting involved, but they are not always sure where to
start. Or they task a few employees to contribute without a good plan on how or
where to do so. I think it will be good having a place to direct these
companies to, to help them understand how to work with OpenStack and an open
source community.

### Troila Gold Member Application

Troila is an IT services company in China that provides a cloud product based
on OpenStack to their customers. They have been using OpenStack for some time
and saw the value in becoming an OSF Gold level sponsor.

As part of the Member Committee, Rob Esker and I met with them the week prior
to go over their application and answer any questions and give feedback. That
preview was pretty good, and Rob and I only had minor suggestions for them to
help highlight what they have been doing with OpenStack and what their future
plans were.

They had taken these suggestions and made updates to their presentation, and I
think they did a very nice job explaining their goals. There was some
discussion and additional questions from the board, but after a quick executive
session, we voted and approved Troila as the latest Gold member of the
OpenStack Foundation.

### Combined Leadership Meeting

The second half of the day was a joint session with the Board and the Technical
Committees or Technical Steering Committees of the OpenStack, StarlingX,
Airship, Kata, and Zuul projects. Each team gave a community update for their
respective areas.

My biggest takeaway from this was that although we are unresources in some
areas, we really do have a large and very active community of people that
really care about the things they are working on. Seeing growing adoption for
things like Kata Containers and Zuul is really exciting.

### Next Meeting

The next meeting will be a conference call on December 10th. No word yet on the
agenda for that, but I wouldn't expect too much being so soon after Shanghai. I
expect their will probably be some buzz about the annual elections coming up.

Once available, the [agenda will be published to the usual
spot](https://wiki.openstack.org/wiki/Governance/Foundation/10December2019BoardMeeting).

I have the issue that I have been able to finish out my term because the rest
of the board voted to allow me to do so as an exception to the two seat per
company limit since I had rejoined Dell half way through the year. That won't
apply for the next election, so if the three of us from Dell all hope to
continue, one of us isn't going to be able to.

I've waffled on this a little, but at least right now, I do think I am going to
run for election again. Prakash has been doing some great work with his
participation in the India OpenStack community, so I will not feel too bad if I
lose out to him. I do think I've been more integrated in the overall
development community, so since an Individual Director is supposed to be a
representative for the community, I do hope I can continue. That will be up to
the broader community, so I am not going to worry about it. The community will
be able to elect those they support, so no matter what it will be good.
