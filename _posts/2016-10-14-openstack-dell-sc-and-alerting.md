---
title: OpenStack, Dell SC, and Alerting
categories:
  - OpenStack
tags:
  - cinder
  - monitoring
  - storagecenter
---

I recently had a good discussion about the “errors” reported from Dell SC
storage when using OpenStack. I hadn’t really thought much about it before, but
this highlighted a difference in functional requirements and expectations
between running a “traditional” IT infrastructure vs running a “cloud”
environment.

First, a little background on the Dell SC alerting I’m referring to.

The SC probably works a little differently than other arrays in this aspect.
Access to volumes are controlled by specifying which initiators have access to
which volumes. That part is probably not too different – most have some sort of
access control list (ACL) support. But the SC does this by creating what is
called a Server Definition and then creating Mapping Profiles to control which
initiators get told about which volumes on SCSI REPORT_LUN calls.

These server definitions define one or more initiators as being part of a
single manageable entity. It models the physical or virtual hosts used to
connect to the storage. Not only does it give the initiators a name and
grouping, but it also defines thing like the operating system running on the
host. This gives the storage some awareness of the capabilities and limitations
of the host for things like LUN restrictions, multipathing support, max volume
sizes, etc.

One setting on this server definition is “Alert On Lost Connectivity” that
tells the array whether to generate email notifications or SNMP traps if any
connectivity between any of the initiators and the storage array go down. This
defaults to enabled.

#### Traditional Infrastructure

For traditional environments, it makes a lot of sense for this alerting to be
enabled. When running an Exchange server or VMware vSphere hosts an admin would
typically create iSCSI sessions from one or more of the hosts initiator ports
to the array, then go the Dell Storage Manager (DSM) and create a server
definition for those new initiators.

This is a one time operation for all servers, and a lot of it is automated
through things like our vSphere Client plugin and other integrations. But once
the sessions are established, it’s just a matter of creating and deleting
mapping profiles, and rescanning on the hosts, to add or remove volumes. Need a
new datastore for your VMs? Just create the volume, map it to your ESX hosts,
and rescan. No data path management needed.

So in this fairly static environment it makes sense to want an email alert at 2
AM if your Exchange database has lost connectivity.

#### Cloud Infrastructure

When running something like OpenStack, however, you definitely don’t want your
storage array waking you up at 2 AM because a host went away. In automated
infrastructures such as this, this kind of thing is expected and quite normal.

At a very high level, when a new Cinder volume is created, Cinder first talks
to the backend driver to create the volume, then asks the driver for attachment
information to give the Nova compute host. Nova then logs in to the provided
targets and looks for the volume.

There’s a couple interesting things here. First, if the Nova host logs in to
one of the provided targets and doesn’t find the device there, it will log back
out. Then later, once a volume is detached through OpenStack, the Nova host
will log out of any targets that are no longer needed.

This dynamic connection handling is perfectly normal for infrastructure that is
being automated by a cloud platform like OpenStack. But it’s a lot different
animal than your somewhat static Exchange and SQL Server environment. In this
case you don’t want to “Alert On Partial Connectivity” since partial
connectivity (and lost connectivity) are to be expected and is the norm.

#### Dell SC Cinder Driver

In order to automate things, if a request comes in to the Cinder driver to
attach to a host and there is no Server Definition found, the Dell SC Cinder
driver will automatically create the server and associate any initiator ports
with it. This makes it simple for OpenStack cloud deployments to just spin up
new Nova compute instances and start using them.

Prior to the Newton release, we hadn’t realized the implication that alerting
is enabled by default. This was unfortunately overlooked in our environments.

Starting with the Newton release we have changed this. One extra parameter is
passed in to the call to create the Server Definition to turn off this flag. So
hopefully users of Newton and later will never even notice this.

If you are using Cinder versions prior to Newton, or have upgraded and had
Server Definitions created by these older versions, it’s a simple manual task
to change this. In the DSM UI, just find the server, right-click and select
Edit Settings, then in the server settings dialog uncheck the Alert On Partial
Connectivity checkbox.

_image lost from migration_

If you are unable to upgrade your Cinder version, but would like to have this
fixed, luckily it is a very minor change. Just add this one line to your local
installation to change the driver behavior:

[Dell SC Driver Patch](https://review.openstack.org/#/c/369770/1/cinder/volume/drivers/dell/dell_storagecenter_api.py)

#### Monitoring

So the question comes up of what to do for monitoring and alerting. It’s
certainly useful to get an email alert if something goes wrong in your
environment. Many, many tools have been created expressly for this purpose. You
don’t want to wander in Monday morning only to realize that you’ve had some
kind of outage since Friday night.

With something like OpenStack though, alerting at the storage array level is
not the right place. You can and will have connections going down, but that’s
completely normal and expected. From the array level, there is no way to know
whether that server was supposed to go away or not. There’s just not the
visibility into the environment needed in order to decide whether something was
intentional or not.

You can (and should!) monitor your network devices. While the array does not
know if a connection going away was on purpose or not, seeing a switch go down
would be a sure fire way of knowing there is an infrastructure issue that needs
your attention. There are a lot of options here from vendor specific tools to
open source monitoring systems. I won’t even attempt to make any
recommendations here as it really depends on your environment and what other
tools you have in place. Most likely you are already using something to do
this, so it may just be a matter of checking with your vendor documentation on
how best to monitor your network fabric.

To effectively monitor a cloud environment, you probably need a monitoring
tools that actually understands the cloud. In order for a storage array to
really be able to alert on a problem, it would need to have more awareness of
what other operations are being performed in the environment (destroying an
instance, migrating a volume) to know whether something going down was
intentional or not. There needs to be something that knows about these
operations and can evaluate whether something was intentional or the result of
a failure somewhere in the stack.

Luckily there is a service designed to do this specifically for OpenStack with
the Monasca project.

I’m not a Monasca expert, and I’m not involved in that project, so I won’t try
to dig in too much there. But if you are looking at monitoring options for your
cloud, that is where I would start looking. To effectively monitor your cloud,
you need something that understands the cloud. I’m very interested in seeing
where this project goes, and if other existing monitoring platforms add support
to address this knowledge gap.
