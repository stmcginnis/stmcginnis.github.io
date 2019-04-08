---
title: April 2019 OpenStack Board Notes
categories:
  - OpenStack
tags:
  - "OSF Board"
---

These are just some of my notes from the OpenStack Foundation Board of
Directors meeting that took place on April 8, 2019.

Upcoming and past OSF board meeting information is
[published on the
wiki](https://wiki.openstack.org/wiki/Governance/Foundation#OpenStack_Board_of_Director_Meetings)
and the meetings are open to everyone. Occasionally there is a need to have a
private, board member only portion of the call to go over any legal affairs
that can't be discussed publicly, but that should be a rare occasion.

This meeting was added to discuss some of the incubating project confirmations
ahead of our next face-to-face "joint leadership" meeting with the Technical
Committee and User Committee on April 28, the Sunday prior to the next Open
Infrastructure Summit in Denver, Co.

## April 8, 2019 OpenStack Foundation Board Meeting

The original [agenda can be found
here](https://wiki.openstack.org/wiki/Governance/Foundation/8April2019BoardMeeting)
and Jonathan Bryce sent out some unofficial minutes to the Foundation mailing
list. The [April 8th notes can be found
here](http://lists.openstack.org/pipermail/foundation/2019-April/002752.html).

### Project Confirmation

After the typical roll-call and approving prior meeting minutes administrative
activities, our first order of business for the day was to review proposals
from the two pilot projects that are ready for confirmation to become full
OpenStack Foundation top level projects: Zuul and Kata Containers.

This was initially supposed to be a walk through of the presentations with part
of the goal of seeing what other information we might need to be able to fully
evaluate them before making anything official. At least in the case of Kata
Containers, we felt there really wasn't anything more needed. So after some
quick discussion of whether to defer until the meeting in Denver, we decided to
hold the vote today. More details below.

These presentations by each team were driven by ansering the factors called out
in the [OSF Project Confirmation
Guidelines](https://wiki.openstack.org/wiki/Governance/Foundation/OSFProjectConfirmationGuidelines).
This was our first time going through a real world case of answering these, and
I think they held up well and ended up being a good way to cover most areas of
concern and guide the discussions.

#### Kata Containers

Eric Ernst from Intel presented for Kata Containers.

> Mission Statement
>> Openly collaborate across a diverse, global community to define and
>> implement secure, versatile container solutions that combine the benefits of
>> virtualization with the performance and ease of containers.

Basically, Kata is a container runtime that leverages lightweight virtual
machines to provide the isolation and security of VMs with the speed and
flexibility of containers.

##### Governance

The Kata Containers community is made up of Contributors and Maintainers, with
an Architecture Committee responsible for overall architectural decisions and
making final decisions if Maintainers disagree. This seems roughly equivalent
to OpenStack's Contributors and Core Reviewers, with the Architecture Committee
equivalent to what some have argued the OpenStack Technical Committee should
really be.

Really great to hear that they do have policies in place that no more than two
members of the AC can be from the same company and that they have already been
holding elections every six months using the same condorcet method CIVS voting
as used in the OpenStack community.

##### Technical Best Practices

Kata has documentation available and it is considered an ongoing and active
focus.

Code contributed to the project goes through peer code review before being
accepted.

Test and CI/CD is enforced on code changes to help ensure quality.

Bug/issue tracking is in place and security VMT is in place.

##### Open Collaboration

I was really glad to see the Kata Community is following the "4 Opens":

> * Open Source
> * Open Design
> * Open Development
> * Open Community

I think this is really the cornerstone of what has united the OpenStack
Community, so my concern with expanding the umbrella of the OpenStack
Foundation was making sure the new projects coming on also believed in these
principles. It's really great to see the Kata Community believes in them too
and considers all four as important elements to an open source project and
community.

##### Active Engagement

Big kudos to the team for the level of involvement they've had with trying to
get out and get active engagement from contributors and users. They've had a
presence at the past OpenStack Summits and will be at the upcoming Open
Infrastructure Summit. They will also be holding sessions at the Project Teams
Gathering following the Summit in Denver.

They've also made an effort to get out to other communities. They've been
attending OpenStack and Open Infra Days events, but have also been active at
Kubecon, KVM-Forum, Open Source Summit, and DevSecCon. All great areas for this
type of project.

##### Voting

All questions were answer openly and clearly, and there really didn't seem to
be much reason to wait to do a vote. Concern was raised by some board members
that we had said we would be doing the vote at the end of the month so we
should stick to what we had said, but after a quick poll of the board members
it was decided to move ahead with the approval vote.

With one abstaining (due to the change in our stated voting plan) we did
approve the Kata Containers confirmation. Kata is now an official full top
level project of the OpenStack Foundation.

#### Zuul

The Zuul CI project grew out of (and in many ways helped shape) the OpenStack
project and community, so this seemed like it would be even less of a
discussion.

##### Presentation

Monty Taylor presented the [slides that the Zuul team has made
available](https://zuul-ci.org/confirmation/). I believe the plan is to keep
that available, so I won't go over each section here. I think it's enough to
say, all the areas that the board is concerned about for confirming a project
have been followed and shaped by the Zuul team well before we even starting
talking about other top level projects.

##### Voting

Voting did hit a snag for this project though. One piece of Zuul, the
[zuul-preview component](https://opendev.org/openstack-infra/zuul-preview) does
get compiled with some GPLv3 libraries. The [OpenStack Governance
Bylaws](https://wiki.openstack.org/wiki/Governance/Foundation/Bylaws#ARTICLE_VII._INTELLECTUAL_PROPERTY_POLICY)
do specifically call out the Apache 2.0 license.

I think it may be a matter of properly phrasing things, but since no one had a
clear answer if the use of GPLv3 was acceptable in this situation, we plan on
working through the legal aspects of this and regroup for further discussion at
the face-to-face meeting in Denver.

#### Bylaws Change

A potential change in the wording of another bit of the bylaws was brought up,
with the plan to actual vote on the change in Denver.

> 4.16 Open Meetings and Records. Except as necessary to protect
> attorney-client privilege, sensitive personnel information, discuss the 
> candidacy of potential Gold Member and Platinum Members, and *discuss the
> review and approval of Open Infrastructure Projects,* the Board of Directors
> shall: (i) permit observation of its meetings by Members via remote
> teleconference or other electronic means, and (ii) publish the Board of
> Directors minutes and make available to any Member on request other
> information and records of the Foundation as required by Delaware Corporate
> Law.

This was in reaction to past meetings where it was brought up that some board
members may have some reasons where they can't speak publicly about a project
or backing company being considered and would need a private "closed door"
session to be able to raise their concerns.

Setting aside for now that I'm against doing any of the new project evaluations
behind closed doors, I did raise on the mailing list before the meeting that I
would really rather this be reworded to make sure it is clear that these
meetings would only be an exception basis, and as much as possible all
discussions about new projects should be done in the open for all to see. The
way this is worded now, to me at least, reads like these meetings are expected
to be done in private. I know that is not the intent of everyone involved right
now, but I would rather have that intent made clear in the way this is added in
the bylaws versus just leaving it open for interpretation.

I'm sure there will be plenty more discussion around the wording, and even the
for this before we meet in Denver.
