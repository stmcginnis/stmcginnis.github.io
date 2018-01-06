---
title: OpenStack - Using provider networks with isolated storage traffic
categories:
  - OpenStack
tags:
  - networking
  - cinder
---

OpenStack Neutron has a configuration option called provider networks that
allows simpler connectivity to existing network infrastructure. This works
great for my lab environment where I just want my VMs to connect directly to my
shared subnets.

There are a lot of tools for deploying OpenStack now (too many?), but I just
used the official install guides for Ubuntu 16.04 found on the
[docs.openstack.org](https://docs.openstack.org/) site.

It’s generally considered a best practice to isolate all storage traffic from
your production traffic. My lab environment looks something like this:

_image lost in migration_

For the basic provider network I had set up, this was fine. Each compute node
was set up with a NIC and IP on each of the “production” and “storage”
networks. Cinder volumes could be mapped to the nova-compute hosts to provide
persistent storage for the VMs. Life was good.

Then I had to complicate it. I wanted to spin up some devstack instances on VMs
in this test cloud to try some things out. But with the default provider
network settings, I only had access to the production subnet, so the VMs could
not reach the iSCSI ports of my array.

This may be fine for some. If your production subnet has routing configured to
be able to get to the iSCSI network, while not ideal, the VM’s storage traffic
can get routed through and you probably won’t be able to tell any difference.
But at least in my case, my iSCSI subnet is completely isolated so this would
not work.

So the trick is to actually configure two provider networks, the default
“provider” network from the installation guide, and a second “iscsi” (or
whatever you want to name it) network to be able to provide your VMs with two
NICs, just like the host nova-network node has.

Again, following the provider setup instructions from the docs, there are just
a couple of tweaks needed to add an additional provider network.

First, in `/etc/neutron/plugins/ml2/ml2_conf.ini` you need to include the name
of the second provider network you would like to include. Again, this can be
whatever you would like to name it. In my case I just called it “iscsi”.

```yaml
[ml2_type_flat]

#
# From neutron.ml2
#

# List of physical_network names with which flat networks can be created. Use
# default ‘*’ to allow flat networks with arbitrary physical_network names. Use
# an empty list to disable flat networks. (list value)
flat_networks = provider,iscsi
```

Then in `/etc/neutron/plugins/ml2/linuxbridge_agent.ini` I had to add the
mapping for that provider network to the local host’s interface.

```yaml
[linux_bridge]

#
# From neutron.ml2.linuxbridge.agent
#

# Comma-separated list of <physical_network>:<physical_interface> tuples
# mapping physical network names to the agent’s node-specific physical network
# interfaces to be used for flat and VLAN networks. All physical networks
# listed in network_vlan_ranges on the server should have mappings to
# appropriate interfaces on each agent. (list value)
physical_interface_mappings = provider:eno1,iscsi:eno2
```

Check your host’s ifconfig information to find the name of the interface that
corresponds to each of your networks.

After following through the rest of the setup instruction, you eventually get
to the point of configuring your networks in OpenStack, with provider network
setup instructions included in the guide.

So after sourcing your admin credentials, you first need to define the network
for your iSCSI traffic:

```
neutron net-create –shared –provider:physical_network iscsi –provider:network_type flat iscsi
```

Then create the subnet:

```
neutron subnet-create –name iscsi –allocation-pool start=10.23.139.200,end=10.23.139.254 iscsi 10.23.139.0/24
```

Of course, you will need to update the values in the allocation-pool to match
your given environment.

Once this was defined, I was then able to launch an instance from this cloud
and allocate both my normal “provider” production network, and my new “iscsi”
storage network.

_image lost in migration_

And that’s about it for configuration on the parent cloud end. Your new VM will
now have two NICs. As long as you add them in the correct order (as above) it
will boot up with access on the provider network.

The one additional step is that by default, the second NIC in the guest VM (at
least in the Ubuntu cloud image I was using) does not get any configuration set
and does not get initialized. I’m sure there are scripts you can run using
cloud-init to automate this, but I was too lazy to do that for now and just
logged into the booted VM and did something like the following:

```
sudo su –
cd /etc/network/interfaces.d
cp eth0.cfg eth1.cfg
vi eth1.cfg   # change reference from eth0 to eth1
ifup eth1
```

The rest is just setting up whatever you would like to run within the nested
VM. Since I set up the iSCSI network to not have a gateway, any local traffic
for my iSCSI subnet gets routed out the eth1 interface, all other traffic gets
routed out eth0. Pretty clean mirror of the physical host configuration it’s
running on.
