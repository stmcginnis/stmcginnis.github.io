---
layout: default
title: SSH Key for Specific Host
---

{{ page.title }}
================

<p class="meta">22 Sept 2014</p>

This information is available elsewhere, but placing here for easy access.

I recently had the need to use a specific key file for an SSH connection. In
this case it was for a git server that needed different credentials than I use
for other hosts.

It turns out it is very easy to configure SSH to use a different key when
connecting to a specific host. Just add the following to your ~/.ssh/config
file.

```
Host git.host.name
Hostname git.host.name
IdentityFile ~/.ssh/special_key
IdentitiedOnly yes
```

That’s it. Now when attempting to establish an SSH connection to git.host.name,
SSH will automatically know to use the key file specified for that host.
