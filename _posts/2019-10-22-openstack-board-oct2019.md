---
title: October 2019 OpenStack Board Notes
categories:
  - OpenStack
tags:
  - "OSF Board"
  - Airship
---

Another OpenStack Foundation Board of Directors meeting was held
October 22, 2019. This meeting was added primarily as to discuss the Airship's
request to for confirmation to become an official project.

The [meeting
agenda](https://wiki.openstack.org/wiki/Governance/Foundation/22October2019BoardMeeting)
is published on the wiki.

### OSF Updates

Jonathan Bryce gave a quick update on the OpenStack Train release that went out
last week. The number of contributors, variety of companies, and overall commit
numbers were pretty impressive. There were over 25,500 merged commits in Train,
with 1,125 unique developers from 165 different organizations.
With commits over the last cycle, OpenStack is still one of the top three
active open source projects out there, after the Linux kernel and Chromium.

Jonathan also reiterated that the event structure will be different starting
in 2020. The first major event planned is in Vancouver, June 8. This will be
more of a collaborative event, so expect the format to be different than past
Summits. I'm thinking more Project Teams Gathering than Summit.

### Airship Confirmation

Matt McEuen, Alex Hughes, Kaspar Skels, and Jay Ahn went through the [Airship
Confirmation
presentation](https://www.airshipit.org/images/airship-confirmation-review-for-the-osf-board.pdf)
and answered questions about the project and their roadmap. Overall, really
pretty impressive what the Airship community has been able to accomplish so
far.

The Airship mission statement is:

> Openly collaborate across a divers, global community to provide and integrate
> a collection of loosely coupled but interoperable, open source tools taht
> declaratively automates cloud lifecycle management.

They started work inside of openstack-helm and kept to the OpenStack community
[Four Opens](https://www.openstack.org/four-opens/) right from the start.

#### Project Diversity

The project was started by AT&T, so there is still a lot of work being done
(code reviews, commits, etc.) from the one company, but the trend over the last
couple of years has been really good, trending towards more and more
contributor diversity.

They also have good policies in place to make sure the Technical Committee and
Working Committee have no more than two members from the same company. Great to
see this policy in place to really encourage more diversity in the spots where
overall project decisions are made. Kudos to the AT&T folks for not only
getting things started, but driving a lot of change while still actively
encouraging others so it is not a one company show. It can be hard for some
companies to realize that giving up absolute control is a good thing,
especially when it comes to an open source community.

#### Community Feedback

Part of the [confirmation
process](https://wiki.openstack.org/wiki/Governance/Foundation/OSFProjectConfirmationGuidelines)
is to make sure the existing OSF projects are comfortable with the new project.
There was feedback from the [Zuul
project](http://lists.openstack.org/pipermail/foundation/2019-October/002801.html)
and from the [OpenStack
TC](http://lists.openstack.org/pipermail/foundation/2019-October/002802.html).
Rico Lin went through the TC feedback in the meeting. Only minor questions or
concerns were raised there, and Matt was able to respond to most of them in the
meeting. He did state he would respond to the mailing list so there was a
record there of the responses.

#### Licensing

Really the only point of concern was raised at the end. One difference between
Airship and other OpenStack projects is that it is written in Go. Go has a
great system built in to be able to easily use modules written by others. But
that led to the question of licensing.

The OSF bylaws state:

> The Board of Directors may approve a license for an Open Infrastructure
> Project other than Apache License 2.0, but such license must be a license
> approved by the Open Source Initiative at the date of adoption of such
> license.

The Airship code itself is Apache 2.0. But there isn't anything done today to
vet the dependencies that are pulled in to actually compile the project. The
concern is the copyleft licenses usually have provisions that if they are
pulled in and linked to non-copyleft code, it then makes that code fall under
the copyleft requirements. So the only concern was that it just wasn't known
what the effective license of the project is today based on what is being
pulled in.

It can be a very tricky area that definitely requires involvement of lawyers
that understand copyright law and open source licensing. Luckily it wasn't a
show stopper. We moved to add the project and have them work with OSF legal to
better understand the licensing impacts and resolve any concerns by using
different dependencies if any are found to be licensed with something that
would impose copyleft into Airship. The board unanimously voted in favor of
Airship becoming a fully official Open Infrastructure Project.

### Next Meeting

The next OSF board meeting will take place November 3rd, in Shanghai, the day
before the Open Infrastructure Summit.
