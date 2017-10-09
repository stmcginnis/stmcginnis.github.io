---
layout: default
title: SSH Key for Specific Host
---

{{ page.title }}
================

<p class="meta">4 Nov 2015</p>

### Will the Real Block Storage Service Please Stand Up

Should Cinder be usable outside of a full OpenStack environment.
There are several solutions out there for providing a Software
Defined Storage service with plugins for various backends. Most
of the functionality used for these is already done by Cinder.
So the question is, should Cinder try to be that ubiquitous SDS
interface?

The concern is that Cinder should either try to address this
broader use case or be left behind. Especially since there is
already a lot of overlap in functionality, and end users already
asking about it.

Some concern about doing this is whether it will be a distraction
from our core purpose – to be a solid and useful service for
providing block storage in an OpenStack cloud.

On the other hand, some folks have played around with doing this
already and found there really are only a few key issues with
being able to use Cinder without something like Keystone. Based on
this, it was decided we will spend some time looking into doing
this, but at a lower priority than our core work.

### Availability Zones in Cinder

Recently it was highlighted that there are issues between AZs
used in Cinder versus AZs used in Nova. When Cinder was originally
branched out of the Nova code base we picked up the concept of
Availability Zones, but the ideas was never fully implemented and
isn’t exactly what some expect it to be in its current state.

Speaking with some of the operators in the room, there were two
main desires for AZ interaction with Nova – either the AZ specified
in Nova needs to match one to one with the AZ in Cinder, or there
is no connection between the two and the Nova AZ doesn’t matter on
the Cinder side.

There is currently a workaround in Cinder. If the config file
value for allow_availability_zone_fallback is set to True, if a
request for a new volume comes in with a Nova AZ not present, the
default Cinder AZ will be used instead.

A few options for improving AZ support were suggested. At least for
those present, the current “dirty fix” workaround is sufficient. If
further input makes it clear that this is not enough, we can look
in to one of the proposed alternatives to address those needs.

### API Microversions

Some projects, particularly Nova and Manila, have already started
work on supporting API microversions. We plan on leveraging their
work to add support in Cinder. Scott D’Angelo has done some work
porting that framework from Manila into a spec and proof of concept
in Cinder.

API microversions would allow us to make breaking API changes while
still providing backward compatibility to clients that expect the
existing behavior. It may also allow us to remove functionality
more easily.

We still want to be restrictive about modifying the API. Just
because this will make it slightly easier to do, it still has
an ongoing maintenance cost, and slightly a higher one at that,
that we will want to limit as much as possible.

A great explanation of the microversions concept was written up by
Sean Dague here:

https://dague.net/2015/06/05/the-nova-api-in-kilo-and-beyond-2/

### Experimental APIs

Building on the work with microversions, we would use that to expose
experimental APIs and make it explicit that they are experimental
only and could be removed at any time, without the normal window
provided with deprecating other features.

Although there were certainly some very valid concerns raised about
doing this, and whether it would be useful or not, general consensus
was that it would be good to support it.

After further discussion, it was pointed out that there really isn’t
anything in the works that needs this right now, so it may be delayed.
The issue there being that if we wait to do it, when we actually do
need to use it for something it won’t be ready to go.

### Cinder Nova Interaction

Great joint session with some of the Nova folks. Talked through some
of the issues we’ve had with the interaction between Nova and Cinder
and areas where we need to improve it.

Some of the decisions were:

- Working on support for multiattach. Will delay encryption support
  until non-encrypted issues get worked out.

- Rootwrap issues with the use of os-brick. Priv-sep sounds like it
  is the better answer. Will need to wait for that to mature before
  we can switch away from rootwrap though.

- API handling changes. A lot of cases where an API call is made and
  it is assumed to succeed. Will use event notifications to report
  results back to Nova. Requires changes on both sides.

- Specs will be submitted for coordinated handling for extending
  volumes.

- Volume related Nova bugs were highlighted. Cinder team will try to
  help triage and resolve some of those.

  https://bugs.launchpad.net/nova/+bugs?field.tag=volumes


### Volume Manager Locks

Covered in cross-project discussion on DLM. Will use tooz as an
abstraction layer. Default will use local locks, so no change for
those that don’t need it, but ability to plug in DLMs like
zookeeper, etc., for those that need the DLM functionlity.

### C-Vol Active/Active HA

Discussed the desire to be able to run the c-vol service in an a/a
configuration. It can kind of be done now with things like pacemaker,
but there are definite issues and is considered risky. The desire
is to build in support to Cinder to be able to run A/A, but we don’t
want to impose heavy requirements on operators running smaller
deployments or deployments that do not require A/A.

Based on the DLM discussion, we should be able to support this based
on end user configuration. If appropriate locking is in place within
the Cinder code and using the tooz locking abstraction, on single
node installations it will just work without extra overhead. If A/A
is desired, configuring tooz to use a distributed locking mechanism
will provide locking across nodes without changes to the code.

### ABC Work

General agreement that our inheritance model is currently a mess
and needs to be cleaned up. Need to work through it. Either collapse
all into a common, simpler, driver base or make our inhertance model
usable. Report capbilities rather than discover via inheritance?

### Cinder Driver Interface

This is a common area of confusion for new driver developers and we
have even found that folks involved with the project for some time
weren’t always clear on what was expected for each call. This is an
attempt to capture those details more clearly without needing to
trace through all of the code to understand the basics.

Eric has started documenting our driver requirements and is making
great progress. This should also help to have a better reference as
we work through the ABC and inheritance cleanup.

### Driver Deadlines

General consensus that past restrictions were a good attempt to bring
some order and focus to work during the cycle, but there were too
many different deadlines to keep track of and it didn’t really solve
all our problems as well as we had hoped.

Based on the discussion, deadlines for the Mitaka cycle have been
adjusted. See the mailing list post for full details:

http://lists.openstack.org/pipermail/openstack-dev/2015-November/078215.html

### Working Session Sprints

We spent the day Friday working through a long list of various topics to
discuss. Some of the highlights from the discussion are:

- Consistency group replication. We are looking in to what this would look
  like now, though there was a lot of concern about adding on top of basic
  replication before we at least have a few drivers implementing it. This
  spec should be used for awareness for the current drivers looking at
  replication support to make sure they are ready for the next step.

- We have consistency groups, but some arrays support concepts such as
  replication groups or even arbitrary grouping of volumes. No changes
  planned at this point, but some ideas being floated around for having a
  more generic volume grouping to support other scenarios.
  Snapshot backup. We support backing up volumes, but we don’t support
  backing up snapshots currently. A spec is in the works to make this a
  supported option.

- Config file setting inheritance. To make it clear what settings are
  inherited by later sections and what are not, considering adding a new
  section to clearly break out what should apply to everything and what
  should be specific to each section.

- We discussed if there was any functionality to pull in to the set of
  minimum required features. Nothing was selected at this time. We may
  add some after all current drivers have more functionality that can
  be considered de facto minimum features.

- We need functional tests. Some work has started, but help is needed to
  expand this. Could be a big win for ensuring better test coverage.

- Ongoing work for conversion to objects and RPC versioning. It was
  agreed this is something we want and it should be completed. This will
  allow rolling upgrades. We plan to support only N-1 upgrades.

- Call out for attention to the LVM driver. Anyone who would like to
  help maintain it – help would be welcome.


### Priorities for Mitaka

- Active/Active High Availability
- Rolling upgrade support
- API Microversions
- Functional testing
- Completion of replication


For the first time, all Design Summit sessions were live streamed and
recorded for later playback. All sessions are available on the
openstack-cinder YouTube channel:

https://www.youtube.com/channel/UCJ8Koy4gsISMy0qW3CWZmaQ

A big thank you to Walter Boring for making this possible.
