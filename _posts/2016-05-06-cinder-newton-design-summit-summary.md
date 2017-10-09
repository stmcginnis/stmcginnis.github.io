---
layout: default
title: Cinder Newton Design Summit Summary
---

{{ page.title }}
================

<p class="meta">6 May 2016</p>

At the Design Summit in Austin, the Cinder team met over three days
to go over a variety of topics. This is a general summary of the
notes captured from each session.

We were also able to record most sessions. Please see the
openstack-cinder YouTube channel for all its minute and tedious
glory:

https://www.youtube.com/channel/UCJ8Koy4gsISMy0qW3CWZmaQ

### Replication Next Steps
Replication v2.1 was added in Mitaka. This was a first step in supporting
a simplified use case. A few drivers were able to implement support for
this in Mitaka, with a few already in the queue for support in Newton.

There is a desire to add the ability to replicate smaller groups of volumes
and control them individually for failover, failback, etc. Eventually we
would also want to expose this functionality to non-admin users. This will
allow tenants to group their volumes by application workload or other user
specific constraint and give them control over managing that workload.

It was agreed that it is too soon to expose this at this point. We would
first like to get broader vendor support for the current replication
capabilities before we add anything more. We also want to improve the admin
experience with handling full site failover. As it is today, there is a lot
of manual work that the admin would need to do to be able to fully recover
from a failover. There are ways we can make this experience better. So before
we add additional things on top of replication, we want to make sure what
we have is solid and at least slightly polished.

Personally, I would like to see some work done with Nova or some third party
entity like Smaug or other projects to be able to coordinate activities on
the compute and storage sides in order to fail over an environment completely
from a primary to secondary location.

Related to the group replication (tiramisu) work was the idea of generic
volume groups. Some sort of grouping mechanism would be required to tie in
to that. We have a grouping today with consistency groups, but that has its
own set of semantics and expectations that doesn’t always fully mesh with
what users would want for group replication.

There have also been others looking at using consistency groups to enable
vendor specific functionality not quite inline with the intent of what
CGs are meant for.

We plan on creating a new concept of a group that has a set of possible types.
One of these types will be consistency, with the goal that internally we can
shift things around to convert our current CG concept to be a group of type
consistency while still keeping the API interface that users are used to for
working with them.

But beyond that we will be able to add things like a “replication” type that
will allow users to group volumes, that may or not be able to be snapped in
a IO order consistent manner, but that can be acted on as a group to be
replicated. We can also expand this group type to other concepts moving
forward to meet other use cases without needing to introduce a wholly new
concept. The mechanisms for managing groups will already be in place and a new
type will be able to be added using existing plumbing.

### Active/Active High Availability
Work continues on HA. Gorka gave an overview of the work completed so far and
the work left to do. We are still on the plan proposed at the Tokyo Summit,
just a lot of work to get it all implemented. The biggest variations are around
the host name used for the “clustered” service nodes and the idea that we will
not attempt to do any sort of automatic cleanup for in-progress work that gets
orphaned due to a node failure.

### Mitaka Recap
Two sessions were devoted to going over what had changed in Mitaka. There were
a lot of things introduced that developers and code reviewers now need to be
aware of, so we wanted to spend some time educating everyone on these things.

_Conditional DB Updates_
To try to eliminate races (partly related to the HA work) we will now use
conditional updates. This will eliminate the gap between checking a value in
setting it, making it one atomic DB update. Better performance than locking
around operations.

_Microversions_
API microversions was implemented in Mitaka. The new /v3 endpoint should be
used. Any change in the API should now be implemented as a micrversion bump.
Devref in Cinder with details of how to use this and more detail as to when
a microversion is needed and when it is not.

_Rolling Upgrades_
Devref added for rolling upgrades and versioned objects. Discussed need to make
incremental DB changes rather than all in one release. First release add new
colume – write to both, read from original. Second release – write to both,
read from new column. Third release – original column can now be deleted.

Recommended service upgrade order: cinder-api, cinder-scheduler, cinder-volume,
cinder-backup. After all services upgraded, send SIGHUP to each to release
version pins.

Multinode grenade tests need to be set up to get regular testing on rolling
upgrades to ensure we don’t let anything through that will cause problems.

Some additional patches are in progress to fix a few internal things like
unversioned dicts that are passed around. This will help us make sure we don’t
change one of those structures in an incompatible way.

### Scalable Backup
The backup service was decoupled from the c-vol service in Mitaka. This allows
us to move backup to other nodes to offload that work. We can also scale out
to allow more backup operations to work in parallel.

Also some discussion of whether KVMs change block tracking could be used to
further optimize this process.

Some other issues were identified with backup. It is currently a sequential
process, so backups can take a long time. Some work is being done to break out
the backup chunks into separate processes to allow concurrent processing.

The idea of having different backup types was brought up. This will require
more discussion. The idea is to have a way to configure different volumes to
be able to be backed up to different backup target types (i.e., one to Swift
backend, one to Ceph backend, one to GCS, etc.).

There are some implications for the HA work being done and how to do clean up.
Those issues will need to be fully identified and worked through.

### Testing Process
We discussed our various available testing and how we can get better test
coverage. We also want to optimize the testing process so everyone isn’t stuck
running long running tests just to validate small changes.

For unit tests, tests that take more than 3-5 seconds to run will be moved out
to our integration tests. Most of these are doing more than just “unit” testing
so makes more sense for them to be separated out.

There’s some desire to gate on coverage, but it was discussed how that can be
a difficult thing to enforce. There was a lot of concern that a hard policy
around coverage would lead to a lot of useless unit tests that don’t really add
valuable testing, just adds coverage over a given path to get the numbers up.

It may be nice to have something like our non-voting pylint jobs to help
detect when our test coverage decreases, but not sure if the additional infra
load to run this would be worth it.

We have a functional test job that is currently non-voting. It has been broken
recently and due to it being non-voting it was missed for some time. This needs
to be fixed and the job changed to be voting. Since the majority of these tests
were moved from the unit tests, they should be fine to make voting once passing
again.

Having in-tree tempest tests were also discussed. We’ve used tempest for
testing things not in line with the mission of tempest. We also have tests in
there that may not really be relevant or needed for other projects getting
them by running all tempest tests. For the ones that are really Cinder specific
we should migrate them from being in tempest to being in tree with Cinder. That
also gives us full control over what is included.

We also discussed plans for how to test API microversions and adding pylint
testing to os-brick.

### CinderClient and OpenStackClient
A group of folks are working through a list of commands to make sure osc has
functional parity with cinderclient. We need to have the same level of support
in osc before we can even consider deprecating the cinder client CLI.

The idea was raised to create a wiki page giving a “lookup” of cinder client
commands to openstackclient commands to help users migrate over to the new
client. Will need to decided how to support this given long term plans for the
wiki, but having the lookup posted somewhere should help.

We do have an extra challenge in that we added a cinderclient extension for
working with os-brick as a stand alone storage management tool. We will work
with the osc team to see what the options are for supporting something like
this and decide what the best end user experience would be for this. It may be
that we don’t deprecate python-cinderclient CLI just for this use case.

### Unconference
Made midcycle plans based on survey results. (Has since changed since we ran in
to complications with hotal availability)

Talked briefly about non-Cinder volume replication and how to recover during
DR. Discussed whether all backends support managing existing volumes. How to
discover available volumes. What to do with snapshots on manage.

### Nova Cross Project
All about multiattach. Matt Riedman wrote up a nice recap of this discussion
already:

http://lists.openstack.org/pipermail/openstack-dev/2016-May/094018.html

##Contributors Meetup
API capability and feature discovery.
What to do for backends that only support allocation in units greater than 1G.
Next steps for user message reporting.
Release priorities:

- User messaging
- Active/active high availability
- Improved functional test coverage
- Better support for cheesecake (repl v2.1)

Extending attached volume.
Discussed qemu-img convert error a couple drivers were seeing.
FICON protocol support.
Generic group discussion.
Force detach.
Replication questions.
QoS support for IOPs per GB.
Per tenant QoS.
Cinder callbacks to Nova for task completion.
Config options inheritance (defined in Default, apply to subsections)
Deadlines (http://releases.openstack.org/newton/schedule.html)
Status of image caching.
Driver interface compliance checks and interface documentation.
