---
title: OpenStack API with PowerShell
categories:
  - OpenStack
tags:
  - "REST API"
  - PowerShell
---

I've been playing around with different platforms, and looking at "native" ways
on those platforms for working with OpenStack. One thing I've been having some
fun with is looking at how to consume the OpenStack REST API's using PowerShell
for Windows.

### PowerShell Background

For those unfamiliar, [PowerShell](https://docs.microsoft.com/powershell/) is
a command line shell and scripting language from Microsoft native to Windows
platforms since Windows 7 and since ported and (partially) made open source a
[couple years
ago](https://azure.microsoft.com/blog/powershell-is-open-sourced-and-is-available-on-linux/).

When I worked on Windows platforms regularly, I loved having the power of
PowerShell (pun intended Chris). Having an object oriented, loosely typed
command line with the entire .Net standard library behind it allowed for doing
a lot of things on Windows that were far beyond the capabilities of the Command
Prompt and a lot easier than firing up Visual Studio to throw together some
throw away code. It has a very nice [verb]-[noun] convention that makes it easy
to understand and a lot more readable than raw code.

### OpenStack REST APIs

OpenStack uses REST and JSON, and nearly all of the OpenStack service have
(mostly) well [documented APIs](https://developer.openstack.org/api-ref/). As
you can see [from the Cinder API
ref](https://developer.openstack.org/api-ref/block-storage/v3/index.html), this
details the endpoints and the request and response parameters.

Using command line tools like python-cinderclient or the OpenStackClient nicely
hides these details. But sometimes you need to do something that is not yet
implemented in the client, or you need to write some custom code to do
something unique. A really nice thing about these clients is you can pass a
`--debug` option and get curl command line examples for every step the CLI is
taking internally.

The curl command is available on most platforms, but not really a standard
thing on Windows. Plus, once you get results back from those calls, you need to
be able to process that data to perform whatever other operations you are
trying to perform.

### PowerShell for OpenStack REST calls

This is where PowerShell comes in. PowerShell has cmdlets ("command-lets" - the
individual commands you can run in PowerShell) that can perform HTTP operations
and handle the results that you get back from the calls.

While there are examples out there of using curl, and there are various
development tutorials, it is not entirely obvious how to make all the necessary
calls to authenticate and perform the different types of actions that the API
exposes.

#### Authenticating

First, we need to get our credentials for our OpenStack cloud. There are
various ways to do this - hard code in the script, read from a file, read
environment variables - but for now I am using the Get-Credential cmdlet to
prompt for them when the script is run:

```powershell
$creds = Get-Credential
```

Then once that is run, we can authenticate against Keystone:

```powershell
$headers = @{"Content-Type" = "application/json"}

$result = Invoke-WebRequest -Headers $headers -Method Post -Body @"
{
  "auth": {
    "identity": {
      "methods": ["password"],
        "password": {
          "user": {
            "name": "$($creds.UserName)",
            "domain": {"id": "default"},
            "password": "$($creds.GetNetworkCredential().password)"
          }
        }
      },
      "scope": {
        "project": {
          "domain": {"name": "Default"},
          "name": "admin"
      }
    }
  }
}
"@ -Uri http://192.168.1.230:5000/v3/auth/tokens
```

This code sets a header (json is the default/only type, but just to be
thorough) and then uses `Invoke-WebRequest` to POST against the /v3/auth/tokens
endpoint. The body passed in the POST is a JSON structure telling keystone we
our "auth"ing, using the "password" method, and providing our credentials for
the cloud. You can see `$creds.UserName` being used for the name value, but
then things get a little more complicated with the password.

PowerShell stores the credential password as a SecureString. So for us to
provide that in the JSON body we are posting, this needs to extract that out
into plain text. That is done via `$creds.GetNetworkCredentials().password`.

You can also see we are setting the project scope for our token to be `admin`
in the `Default` domain.

The final step for authentication is to extract our token from the returned
headers and set in the headers that we will be using for the rest of our calls:

```powershell
$os_token = $result.Headers['X-Subject-Token']
$headers.Add('X-Auth-Token', $os_token)
```

The call to Keystone returns the `X-Subject-Token` header which we need to pass
in subsequent calls as our `X-Auth-Token` header.

#### Making API calls

Now that we have our token and it is set in the headers we will pass along, we
can make actual calls to the service to do what we need to do.

```powershell
$result = Invoke-RestMethod -Headers $headers -Uri "http://192.168.1.230:8776/v3/$project_id/volumes"
foreach($vol in $result.volumes) {
}
```

One thing to point out here is the difference in the cmdlet used to make the
call. When authenticating against Keystone, I used the Invoke-WebRequest
cmdlet. This is a little more "lower layer" and allows access to some of the
raw HTTP info such as the returned headers.

But for the majority of the rest of the calls we need to make, if there is any
kind of return value other than the HTTP response code, it is going to be some
JSON formatted body. PowerShell has the `Invoke-RestMethod` cmdlet that hides
some of the complexity of processing the raw HTTP response body and gives us
access directly to the JSON objects returns.

So in this example, you can see in the `foreach` loop I am able to directly
iterate on `$result.volumes` to go through each volume object returned from
Cinder.

That is a simple GET request, but performing some sort of action using a POST
is pretty similar, just with the addition of passing in a JSON body:

```powershell
$result = Invoke-RestMethod -Headers $headers -Method Post -Body @"
{
  "snapshot": {
    "name": "demo-snap",
    "description": "Demo snapshot for testing",
    "volume_id": "$src_vol",
    "force": "True"
  }
}
"@ -Uri "http://192.168.1.230:8776/v3/$project_id/snapshots"

$snapshot = $result.snapshot
```

In this case, I've set `$src_vol` to a volume I wanted to snapshot, then passed
in a name and description for the snapshot to create from that volume. The
required structure is all documented in the [Cinder API
reference](https://developer.openstack.org/api-ref/block-storage/v3/index.html#create-a-snapshot).

Then, since `Invoke-WebRequest` makes it easy for us, we can just use
`$result.snapshot` to get the returned snapshot object.

#### Microversioned API calls

Many OpenStack projects use what is called *microversions*. This allows for
changes to a stable API without breaking existing users. By the calling client
specifying which microversion they want, they are telling the services what
their expected functional behavior should be from the API.

The microversion is requested by another header calls `OpenStack-API-Version`.

```powershell
$headers.Add("OpenStack-API-Version", "volume 3.43")
$result = Invoke-RestMethod -Headers $headers -Method Post -Body @"
{
  "attachment": {
    "instance_uuid": "$([guid]::NewGuid())",
    "connector": {
      "initiator": "$((Get-InitiatorPort).NodeAddress)",
      "ip": "$($my_ip.IPAddress)",
      "platform": "x86_64",
      "host": "$env:computername",
      "os_type": "windows",
      "multipath": "true"
    },
    "volume_uuid": "$($volume.id)"
  }
}
"@ -Uri "http://192.168.1.230:8776/v3/$project_id/attachments"
```

So in this case, by providing `volume 3.43` as the value of
`OpenStack-API-Version`, we are telling Cinder to use the 3.43 version of the
API. The API could be changed in later releases, but if our calling code passes
this in, then we are given assurances that we will still get the same behavior
we expect.

In this case, that is passed as part of the headers provided to the call to
create a new volume attachment. I am directly accessing the volume from a
Windows host, not using Nova, so this just passes in some local host iSCSI
initiator and IP address information.

We can then use things like `New-IscsiTargetPortal`, `Connect-IscsiTarget`, and
other Windows storage management APIs to connect and access our volume.

#### Logging Out

Once we are all done, it's a good idea to explicitly log out to invalidate the
token we were provided. That's as simple as passing in the following:

```powershell
$headers.Add('X-Subject-Token', $os_token)
$result = Invoke-WebRequest -Headers $headers -Method Delete `
    -Uri http://192.168.1.230:5000/v3/auth/tokens
```

#### Summary

Any tool that allows making HTTP requests and accessing the headers can be used
as one option for consuming the OpenStack REST API. The details here could also
be used as a pseudo-code style guide for implementing some of these calls in
your programming language of choice. But I think for Windows platforms at
least, PowerShell is a tool that is readily available and makes an easy way to
script some cloud management tasks.

There are a few PowerShell OpenStack SDK projects out there, but since most I
found looked stale or dead, I don't want to link to them here. But if you are
looking for more, there are examples out there if you look. 

It would also be possible to build up some helper functions for many of these
repetitive tasks, making loading a PowerShell module and running some basic
commands very simple.
