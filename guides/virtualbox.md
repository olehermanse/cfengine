# VirtualBox Virtual Machine(VM) Setup

## Download
Download Virtualbox and OS disk image.

## Create VM in VirtualBox
New -> Linux -> Red Hat 64
-> Find the OS (CentOS) disk image in the file system
Use all default settings
Go through OS Setup - Pick a hard disk etc.

## Networking
```
$ nmtui
```
Edit a connection -> Edit -> Automatically Connect (Space) -> Exit
```
$ service network restart
```
Close VM
Machine Settings -> Network -> Adapter 2 -> Enable -> Host only
Start VM
```
$ ip a
```

## User account
```
adduser username
passwd username
usermod -aG wheel username
```

## Keyboard
Show what keyboard locale is in use:
```
localectl status
```
See what Norwegian keyboard settings are available:
```
localectl list-keymaps | grep no
```
Change to Norwegian mac keyboard:
```
localectl set-keymap no-mac
localectl set-x11-keymap no-mac
```

## Nano editor
Install nano editor using yum:
```
sudo yum install nano
```

## VirtualBox Clone
Exit VM
Right click new VM -> Clone -> Enable "Reinitialize MAC..."

## New network config file for static IP:
```
$ sudo nano /etc/sysconfig/network-scripts/ifcfg-enp0s8
DEVICE="enp0s8"
BOOTPROTO=static
IPADDR=192.168.56.99
ONBOOT="yes"
NM_CONTROLLED="no"
NAME="enp0s8"
$ sudo reboot -n
```

# Errors
```
[root@localhost ~]# sudo /var/cfengine/bin/cf-agent --bootstrap 192.168.56.101
  notice: Q: ".../systemctl rest": Failed to open /dev/tty: No such device or address
Q: ".../systemctl rest": Failed to open /dev/tty: No such device or address
R: Bootstrapping from host '192.168.56.101' via built-in policy '/var/cfengine/inputs/failsafe.cf'
R: This host assumes the role of policy server
R: Updated local policy from policy server
R: Started the server
R: Started the scheduler
  notice: Bootstrap to '192.168.56.101' completed successfully!
```

# Debugging
```
cf-agent -K -f ./acl_fail.cf  -d | less
which cf-agent
file ~/cfengine/core/cf-agent/cf-agent
less ~/cfengine/core/cf-agent/cf-agent
libtool --mode=execute gdb --args   cf-agent/cf-agent  -K -f ./tests/acceptance/10_files/12_acl/acl_fail.cf
cf-agent -K -f ./tests/acceptance/10_files/12_acl/acl_fail.cf  -d | less
git grep -C5 -n 'copying body'
```
copying body acl
