# Goals

This project's toplevel goal is to create base *bootable* container images
from Fedora ELN and CentOS Stream packages.

## Status

This is an in-development project not intended for production use yet.

This project was migrated from [a gitlab repo](https://gitlab.com/centos/cloud/sagano)
and currently container images point to that repository.  Reworking
the build system is in progress!

## Trying it out

See [install.md](./install.md).

## Differences from Fedora CoreOS

Fedora CoreOS today is not small; there are multiple reasons for this, but
primarily because it was created in a pre-bootable-container time.  Not everyone
wants e.g. moby-engine.

But going beyond size, the images produced by this project will focus
on a container-native flow.  We will ship a (container) image that does not
include Ignition for example.

## Differences from RHEL CoreOS

We sometimes say that RHEL CoreOS [has FCOS as an upstream][1] but this is only
kind of true; RHEL CoreOS includes a subset of FCOS content, and is lifecycled
with OCP.

An explicit goal of this project is to produce bootable container images
that can be used as *base images* for RHEL CoreOS; for more on this, see e.g.
<https://github.com/openshift/os/issues/799>

## Differences from RHEL for Edge

It is an explicit goal that Sagano also becomes a "base input" to RHEL for Edge.

## What does Sagano means

From [Wikipedia](https://en.wikipedia.org/wiki/Bamboo_Forest_(Kyoto,_Japan)):

> Bamboo Forest, Arashiyama Bamboo Grove or Sagano Bamboo Forest, is a natural
> forest of bamboo in Arashiyama, Kyoto, Japan

[1]: https://github.com/openshift/os/blob/master/docs/faq.md#q-what-is-coreos

## Demonstration base images for Project Sagano

These images are technology demonstrators, not for production use. The
intention is that these images are generated by the OS vendor or
distribution.  
Or, you can fork this repository and generate your own via
`rpm-ostree compose image`.

## Operating system sources

At the moment these demonstration builds use Fedora ELN and CentOS Stream 9.

## Tiers

### Tier 0

This is the basic tier; it has effectively just:

- kernel systemd selinux-policy-targeted bootc

You are generally going to need to generate derived images from this; installing
it on its own will boot to a system with no automatic networking support, no SSH,
and no default passwords etc.

### Tier 1

This is larger system.

- NetworkManager, chrony
- rpm-ostree (to install packages and in case it's useful "day 2")
- openssh-server

At the current time, it does not include Ignition or cloud-init; so you will
still need to derive from it in order to inject a mechanism to log in in many
cases.  However, it will work to install it using e.g. Anaconda and set up
users and passwords that way.

## Image matrix (Fedora)

- `registry.gitlab.com/centos/cloud/sagano/fedora-boot-tier-0:eln`
- `registry.gitlab.com/centos/cloud/sagano/fedora-boot-tier-1:eln`

## Image matrix (CentOS Stream 9)

- `registry.gitlab.com/centos/cloud/sagano/centos-boot-tier-0:stream9`
- `registry.gitlab.com/centos/cloud/sagano/centos-boot-tier-0-rt:stream9`
  (realtime kernel)
- `registry.gitlab.com/centos/cloud/sagano/centos-boot-tier-1:stream9`
- `registry.gitlab.com/centos/cloud/sagano/centos-boot-tier-1-rt:stream9`
  (realtime kernel)

### More about image sources

The current manifest definitions [tier-0](tier-0) and [tier-1](tier-1) were
forked from Fedora CoreOS, but significantly cut down.

The existing content set is obviously subject to change and debate.

## Building

Here's an example command:

```shell
sudo rpm-ostree compose image --authfile ~/.config/containers/myquay.json --cachedir=cache -i --format=ociarchive centos-tier-0-stream9.yaml centos-tier-0-stream9.ociarchive
```

In some situations, copying to a local `.ociarchive` file is convenient. You
can also push to a registry with `--format=registry`.

More information at <https://coreos.github.io/rpm-ostree/container/>

## Badges

| Badge                   | Description          | Service      |
| ----------------------- | -------------------- | ------------ |
| [![Renovate][1]][2]     | Dependencies         | Renovate     |
| [![Pre-commit][3]][4]   | Static quality gates | pre-commit   |

[1]: https://img.shields.io/badge/renovate-enabled-brightgreen?logo=renovate
[2]: https://renovatebot.com
[3]: https://img.shields.io/badge/pre--commit-enabled-brightgreen?logo=pre-commit
[4]: https://pre-commit.com/
