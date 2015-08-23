# dantrell-gnome-reparent

Dantrell B.'s [GNOME without systemd](https://github.com/dantrell/gentoo-project-gnome-without-systemd) overlays provide users with convenient profiles that inherit from `default/linux/<arch>/13.0` in Gentoo. However, because this chosen parenting point is quite 'high up the tree', many of the USE flags etc. that GNOME users on Gentoo might expect to be present are _not_ active globally (`X`, `alsa` etc.).

This (trivial) overlay corrects the issue, by providing new profiles that inherit from _both_ Dantrell's variants _and_ `default/linux/<arch>/13.0/desktop/gnome`, and which may be used in place of the Dantrell profiles on Gentoo.

## Prerequisites

It is assumed that you have already installed Dantrell's overlays and are using one of the profiles it provides. Instructions for setting this up on Gentoo may be found [here](https://wiki.gentoo.org/wiki/GNOME/GNOME_Without_systemd).

## Installation

As of version >= 2.2.16 of Portage, **dantrell-gnome-reparent** is best installed (on Gentoo) via the [new plug-in sync system](https://wiki.gentoo.org/wiki/Project:Portage/Sync).

The following are short form instructions. Begin by creating a custom `/etc/portage/repos.conf` entry for the **dantrell-gnome-reparent** overlay, so Portage knows what to do. Make sure that `/etc/portage/repos.conf` exists, and is a directory. Then, fire up your favourite editor:

    # nano -w /etc/portage/repos.conf/dantrell-gnome-reparent.conf

and put the following text in the file:
```
[dantrell-gnome-reparent]

# Trivial overlay to reparent the dantrell-gnome profiles
# to bring in Gentoo's standard GNOME USE flags, masks etc., and
# thereby maximize familiarity for the end user.
# Maintainer: sakaki (sakaki@deciban.com)
 
location = /usr/local/portage/dantrell-gnome-reparent
sync-type = git
sync-uri = https://github.com/sakaki-/gentoo-b3-reparent.git
priority = 200
auto-sync = yes
```
Then run:
```
# emaint sync --repo dantrell-gnome-reparent
```
The modified profiles should now be visible, e.g.:
```
# eselect profile list
Available profile symlink targets:
  [1]   default/linux/amd64/13.0
  [2]   default/linux/amd64/13.0/selinux
... etc ...
  [20]  default/linux/uclibc/amd64
  [21]  hardened/linux/uclibc/amd64
  [21]  dantrell-gnome:default/amd64/3.14 *
  [22]  dantrell-gnome:default/amd64/3.16
  [23]  dantrell-gnome-reparent:default/linux/amd64/13.0/desktop/gnome/3.14
  [24]  dantrell-gnome-reparent:default/linux/amd64/13.0/desktop/gnome/3.16
```
Now you can set the the desired profile, for example:
```
# eselect profile set "dantrell-gnome-reparent:default/linux/amd64/13.0/desktop/gnome/3.14"
```
With that done, you can then re-emerge @world to pick up the new USE flags etc:
```
# emerge --deep --with-bdeps=y --newuse --update --ask --verbose @world
```
Done!

## Known Issues and Workarounds

The `dantrell-gnome` profiles turn on the `gtkstyle` USE flag by default. This flag is, however, **off** by default in the equivalent Gentoo GNOME profiles, and, as having it on can cause circular dependency loops when building out a fresh system, I have disabled it again in `dantrell-gnome-reparent`.

If you like, it is a simple matter (once you have a working system) to set the flag where necessary via `/etc/portage/package.use`, and re-emerge any affected packages. This should not be an issue for most users.

Because of this change, it is no longer necessary to do any `--oneshot` `emerge` steps when using either of the `dantrell-gnome-reparent` profiles.

(Thanks to [iemanuilov](https://github.com/iemanuilov) for reporting!)

## Maintainers

* [sakaki](mailto:sakaki@deciban.com)
