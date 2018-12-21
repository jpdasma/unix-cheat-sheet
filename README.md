# \*nix Cheat Sheet

A cheat sheet for managing Linux, BSD, and illumos system

## 1. Table of Contents

1. [Table of Contents](#1-table-of-contents)
2. [Service Management](#2-service-management)
3. [User Management](#3-user-management)
4. [Package Management](#4-package-management)
5. [Network Management](#5-network-management)
6. [Others](#6-others)
7. [Useful Resources](#7-useful-resources)

## 2. Service Management

[Back to table of contents.](#1-table-of-contents)

All of the commands involving the modification of a service state assume you are root or have proper rights either using sudo, doas (OpenBSD), or pfexec (illumos).

Majority of Linux distributions are using systemd.

BSDs are using the BSD style init. Aside from the command for FreeBSD and OpenBSD, you can also edit /etc/rc.conf or /etc/rc.conf.local.

illumos distributions are using the Service Management Facitly (smf).

|| Linux (systemd) | FreeBSD | OpenBSD | illumos
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
3. **zypp** and **rpm** (SUSE family) - Integrates with **snapper** and **BTRFS** to create system snapshots before and after using **zypp**.

BSDs also uses different package manager but they also have the ports system.

1. **pkgng** (FreeBSD)
2. **pkgsrc** (NetBSD)
3. **pkg** (OpenBSD)

illumos distributions mostly uses the Image Packaging System (IPS).

1. **ips** (OpenIndiana and OmniOS CE) - Integrates with **beadm** and **ZFS** to create boot environments after using **ips**.
2. **pkgsrc** (SmartOS, also works with other illumos Distribution and the provide repositories for  BSD, and Linux Distributions)
3. **zap** (Tribblix)

The table will only include **yum**, **apt**, **zypp**, **pkgng**, **pkgsrc**, **pkg**, and **ips**. For NetBSD, you would need to install **pkgin** separately via `pkg_add ${remote_url}/pkgin`.

|| CentOS | Debian | OpenSUSE | FreeBSD | NetBSD | OpenBSD | OpenIndiana / OmniOS Community Edition |
| --- | --- | --- | --- | --- | --- | --- | ---
| Installing a package  | `yum install nginx` | `apt-get install nginx` | `zypper install nginx` | `pkg install nginx` | `pkgin install nginx` | `pkg_add nginx` | `pkg install nginx`
| Removing a package | `yum remove nginx` | `apt-get remove nginx` | `zypper remove` | `pkg delete nginx` | `pkgin remove nginx` | `pkg_delete nginx` | `pkg uninstall nginx`
| Update package cache | `yum check-update` | `apt-get update` | `zypper  refresh` | `pkg update` | `pkgin update` | - | `pkg refresh`
| Updating installed packages | `yum update` | `apt-get upgrade` | `zyyper update` | `pkg upgrade` | `pkgin full-upgrade` | `pkg_add -uU` | `pkg update`

## 5. Network Management

### Tools

- **iproute2** - Linux (**ifconfig** can be used, but deprecated)
- **ifconfig** - BSDs
- **crossbow** - illumos (**ifconfig** can be used)

### Notes

- For Linux and BSDs, you would usually need to edit a file to make changes to interfaces persistent.
- For illumos, using **ipadm** and **dladm** is enough

|| Linux | BSDs | illumos
| --- | --- | --- | ---
| List IP Address | `ip addr show` | `ifconfig` | `ipadm show-addr`
| List all interface | `ip link show` | `ifconfig -a` | `dladm show-link`

## 6. Others

### Other commands

|| Linux | BSD | Illumos
| --- | --- | --- | ---
| List open files | `lsof` | `fstat` | `pfiles <pid>`

### Kernel Modules

- For NetBSD, you can only load a kernel module when running at kernel level -1 or 0 or during boot. Thus, it is recommended to load the module during boot.
- OpenBSD does not support kernel modules.

|| Linux | FreeBSD | NetBSD | OpenBSD | illumos
| --- | --- | --- | --- | --- | ---
| List hardwares attached | `lspci` | `pciconf -l` | `pcictl list` | `pcidump` | `scanpci`
| Load kernel module | `modprobe module_name` | `kldload module_name` | `modload module_name` | - | `modload drv/module_name`
| Load kernel module at boot | /etc/modules | /boot/loader.conf | /etc/modules.conf | - | /etc/system
| List loaded kernel modules | `lsmod` | `kldstat` | `modstat` | - | `modinfo`
| Path of kernel modules | /lib/modules/\* | /boot/kernel/\* | /stand/\* | - | /kernel/\*

### Limiting resource and kernel tuning

- For implementing resource limits, checkout the following path (and see their manpages)

| System | File Path 
| --- | --- 
| Linux | `/etc/security/limits.conf, systemd unit files
| BSD | `/etc/login.conf`
| Illumos | `/etc/project,/etc/system`

- For kernel tuning, checkout the following path (and see their manpages)

| System | File Path 
| --- | --- 
| Linux | `/etc/sysctl.conf`
| BSD | `/etc/sysctl.conf`
| Illumos | `/etc/system`

### FreeBSD Jails using NAT and PF

1. Setup cloned interfaces.

```
cloned_interfaces="lo1"
ipv4_addrs_lo1="192.168.0.1-9/24"
```

2. Configure PF.

```
ext_if=vtnet0          # Replace this with the interface with internet access
local_net=lo1:network  # This would be the network for you jails


# Outbound
nat on $ext_if from $local_net to any -> ($ext_if)  # Enable NAT
pass from $local_net to any keep state              # This will enable jails to access the internet

# Inbound
rdr on $ext_if proto tcp from any to ($ext_if) port 2222 -> $ip_in_jails port 22
pass in proto tcp to any port 2222
```

3. Enable PF.

```
pf_enable="YES"
pf_flags=""
pf_rules="/etc/pf.conf"

gateway_enable="YES"
```

### Illumos Zones using NAT and IPF

1. Setup etherstub. This will act as the "virtual" switch.

```
[root@host ~]# dladm create-etherstub stub0
[root@host ~]# dladm create-vnic -l stub0 z1   # This will be the interface for your zone
[root@host ~]# dladm create-vnic -l stub0 g0   # This will be the interface for your host which will act as the gateway
```

2. Assign IP address to g0.

```
[root@host ~] # ipadm create-if g0
[root@host ~] # ipadm create-addr -T static -a 192.168.0.1 g0/v4
```

3. Create your zone.

```
[root@host ~] # zonecfg -z z1
z1: No such zone configured
Use 'create' to begin configuring a new zone.
zonecfg:z1> create
zonecfg:z1> set brand=sparse
zonecfg:z1> set ip-type=exclusive
zonecfg:z1> set zonepath=/zone/z1
zonecfg:z1> add net
zonecfg:z1:net> set physical=z1
zonecfg:z1:net> set allowed-address=192.168.0.2
zonecfg:z1:net> set defrouter=192.168.0.1
zonecfg:z1:net> end
zonecfg:z1> verify
zonecfg:z1> commit
zonecfg:z1> exit
```

4. Configure ipf.conf and ipnat.conf

```
# ipf.conf

pass in on g0 from any to any
pass out on g0 from any to any

# ipnat.conf

map vioif0 192.168.0.0/24 -> 0/32 portmap tcp/udp auto
```
5. Install and start your zone.

## 7. Useful Resources

1. [Arch Wiki](https://wiki.archlinux.org/)
2. [FreeBSD Handbook](https://www.freebsd.org/doc/handbook/book.html)
3. [NetBSD Guide](https://www.netbsd.org/docs/guide/en/)
4. [OpenBSD FAQ](https://www.openbsd.org/faq/)
5. For illumos based distributions, Oracle Solaris<sup>TM</sup> documentation can still be used for majority of it's feature.
6. [The Linux-to-SmartOS Cheat Sheet](https://wiki.smartos.org/display/DOC/The+Linux-to-SmartOS+Cheat+Sheet) - For Linux and Illumos
