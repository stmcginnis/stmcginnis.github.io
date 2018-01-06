---
title: "Cinder Client - absolute-limits"
categories:
  - OpenStack
tags:
  - cinder
  - cinderclient
---

<p class="meta">6 Feb 2017</p>

This is the first of what will hopefully be a series of posts covering the CLI
commands available in the python-cinderclient package.

It is also a long term goal to deprecate the Cinder CLI in favor of the newer
OpenStack Client CLI (osc). The goal of the OpenStack Client is to unify the
various project CLIs into one, consistent, CLI interface. Whenever possible,
it’s highly recommended you learn – and get used to – using the osc CLI
instead.

#### Command:

> cinder absolute-limits

#### Description:

The absolute-limit command gets limits for a user.

#### Details:

This is kind of an odd command. The best I can tell, this command, and the API
in the service it calls, were carried over from when the Cinder project was
split out of Nova. It has since changed in Nova, but the Cinder command remains
the same.

One of the odd things about the command is it gets a mix of different settings
that apply for the current user. Half of the results are the quota limits, half
are API rate limits. These are really to wildly different concepts. They also
each have their own separate API and CLI for getting the same information.

Another interesting thing with the rate limited – it doesn’t appear to work
right now. Or at least I can’t figure out how to correctly configure it. I’ve
filed a bug to make sure that gets figured out or documented.

```
$ cinder absolute-limits
 +--------------------------+-------+
 | Name                     | Value |
 +--------------------------+-------+
 | maxTotalBackupGigabytes  | 1000  |
 | maxTotalBackups          | 10    |
 | maxTotalSnapshots        | 10    |
 | maxTotalVolumeGigabytes  | 1000  |
 | maxTotalVolumes          | 10    |
 | totalBackupGigabytesUsed | 0     |
 | totalBackupsUsed         | 0     |
 | totalGigabytesUsed       | 1     |
 | totalSnapshotsUsed       | 0     |
 | totalVolumesUsed         | 1     |
 +--------------------------+-------+
```

Since I don’t have any rate limits working, the only results are the quota
limits for my current user account. This would be the same as:

```
$ cinder quota-show $PROJECT_ID
 +----------------------------------------+-------+
 | Property                               | Value |
 +----------------------------------------+-------+
 | backup_gigabytes                       | 1000  |
 | backups                                | 10    |
 | gigabytes                              | 1000  |
 | gigabytes_DellStorageCenterISCSIDriver | -1    |
 | per_volume_gigabytes                   | -1    |
 | snapshots                              | 10    |
 | snapshots_DellStorageCenterISCSIDriver | -1    |
 | volumes                                | 10    |
 | volumes_DellStorageCenterISCSIDriver   | -1    |
 +----------------------------------------+-------+
```

Basically the same information (plus per volume type) formatted a little
differently. And the extra inconvenience that you need to pass in $PROJECT_ID.

#### OpenStack Client Equivalent

The OpenStack Client makes these calls consistent for multiple projects and
separates out the quota limit from the rate limit information, which makes a
lot of sense to me.

First, for quota related limit information, it combines relevant data from both
Cinder, Neutron, and Nova into one common list of limits:

```
$ openstack limits show --absolute
 +--------------------------+-------+
 | Name                     | Value |
 +--------------------------+-------+
 | maxServerMeta            |   128 |
 | maxTotalInstances        |    10 |
 | maxPersonality           |     5 |
 | totalServerGroupsUsed    |     0 |
 | maxImageMeta             |   128 |
 | maxPersonalitySize       | 10240 |
 | maxTotalRAMSize          | 51200 |
 | maxServerGroups          |    10 |
 | maxSecurityGroupRules    |    20 |
 | maxTotalKeypairs         |   100 |
 | totalCoresUsed           |     1 |
 | totalRAMUsed             |  2048 |
 | maxSecurityGroups        |    10 |
 | totalFloatingIpsUsed     |     0 |
 | totalInstancesUsed       |     1 |
 | maxServerGroupMembers    |    10 |
 | maxTotalFloatingIps      |    10 |
 | totalSecurityGroupsUsed  |     1 |
 | maxTotalCores            |    20 |
 | totalSnapshotsUsed       |     0 |
 | maxTotalBackups          |    10 |
 | maxTotalVolumeGigabytes  |  1000 |
 | maxTotalSnapshots        |    10 |
 | maxTotalBackupGigabytes  |  1000 |
 | totalBackupGigabytesUsed |     0 |
 | maxTotalVolumes          |    10 |
 | totalVolumesUsed         |     1 |
 | totalBackupsUsed         |     0 |
 | totalGigabytesUsed       |     1 |
 +--------------------------+-------+
```

Then any set rate limits are shown separately with the command:

```
$ openstack limits show --rate
```

Which again is nothing in my case. No output is shown if no rate limit settings are found.
