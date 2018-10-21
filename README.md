# \*nix Cheat Sheet

A cheat sheet for managing Linux, BSD, and Illumos system

## 1. Table of Contents

1. [Table of Contents](#1-table-of-contents)
2. [Service Management](#2-service-management)
3. [User Management](#3-user-management)
4. [Package Management](#4-package-management)
5. [Network Management](#5-network-management)
6. [Others](#6-others)

## 2. Service Management

[Back to table of contents.](#1-table-of-contents)

All of the commands involving the modification of a service state assume you are root or have proper rights either using sudo, doas (OpenBSD), or pfexec (Illumos).

Majority of Linux distributions are using systemd.

BSDs are using the BSD style init. Aside from the command for FreeBSD and OpenBSD, you can also edit /etc/rc.conf or /etc/rc.conf.local.

Illumos distributions are using the Service Management Facitly (smf).

|| Linux (systemd) | FreeBSD | OpenBSD | Illumos
| --- | --- | --- | ---  | ---
| Enable a service | `systemctl enable nginx` | `sysrc nginx_enable=YES` | `rcctl enable nginx` | `svcadm enable nginx`
| Disable a service | `systemctl disable nginx` | `sysrc nginx_enable=NO` | `rcctl disable nginx` | `svcadm disable nginx`
| Starting a service | `systemctl start nginx` | `service nginx start` | `rcctl start nginx` | `svcadm enable nginx`
| Stopping a service | `systemctl stop nginx` | `service nginx stop` | `rcctl stop nginx` | `svcadm disable nginx`
| List running services | `systemctl list-units` | - | `rcctl ls started` | `svcs`
| List all services | `systemctl list-unit-files` | `service -l` | ` rcctl ls all` | `svcs -a`
| Check the status of a service | `systemctl status nginx` | `service nginx status` | `rcctl status nginx` | `svcs -xv nginx`



## 3. User Management

## 4. Package Management

[Back to table of contents.](#1-table-of-contents)

Linux distributions uses different package manager.

1. **apt** and **dpkg** (Debian family)
2. **yum** / **dnf** and **rpm** (Red Hat family)
3. **zypp** and **rpm** (SUSE family)

BSDs also uses different package manager but they also have the ports system.

1. **pkgng** (FreeBSD)
2. **pkgsrc** (NetBSD)
3. **pkg** (OpenBSD)

Illumos distributions mostly uses the Image Packaging System (IPS).

1. **ips** (OpenIndiana and OmniOS CE)
2. **pkgsrc** (SmartOS, also works with other Illumos Distribution and the provide repositories for  BSD, and Linux Distributions)
3. **zap** (Tribblix)

The table will only include yum, apt, pkgng, pkgsrc, pkg, and ips.

|| Red Hat | Debian | FreeBSD | NetBSD | OpenBSD | OpenIndiana / OmniOS Community Edition |
| --- | --- | --- | --- | --- | --- | ---
| Installing a package  | `yum install nginx` | `apt-get install nginx` | `pkg install nginx` | `pkgin install nginx` | `pkg_add nginx` | `pkg install nginx`
| Removing a package | `yum remove nginx` | `apt-get remove nginx` | `pkg delete nginx` | `pkgin remove nginx` | `pkg_delete nginx` | `pkg uninstall nginx`


## 5. Network Management

## 6. Others
