# Networking

port 1/0/37

perched@10.0.0.1

perched1234!@#$

---

## Set Up DHCP

en

config t

setup dhcp

ip dhcp excluded-address 10.0.100.0 10.0.100.1

ip dhcp pool 100

network 10.0.100.0 255.255.255.252

default-router 10.0.100.1

dns-server 192.168.2.1

exit

---

## Setup Vlans

vlan 100

name sg01

state active

no shut

---

## Interfaces

interface Ge 1/0/37

switchport

switchport access vlan sg01

no shut

exit

---

interface vlan sg01

ip address 10.0.100.1 255.255.255.252

no shut

exit

---

## Static Routes

ip routing

ip route 172.16.100.0 255.255.255.0 10.0.100.2

---

# PFSense

## Hardware setup

ESC - enter BIOS Setup
F11 - boot menu

## Initial Install

A - accept
I - Install
 - default US keymap
Auto - Guided Disk Setup
 - select defaults
Finished = reboot

## Intial Config
1 - assign interface
n - skip vlan setup
 - COM = local
 - LAN1 = em0 = WAN
 - LAN2 = em1 = LAN
 y - proceed

reloading

2 - set interface ip address

 - 1 WAN interface
 - y -dhcp
 - n -dhcp6
 - blank for none
 - y doe http webconfig protocol

---
 - 2 LAN interfacea
 - 172.16.100.1/24
 - enter for none
 - enter for none - disable ipv6
 - 172.16.100.100 - range start ; 172.16.100.254 range stop
 - y - http webconfig protocol

 ---

 set laptop to 172 network for webui

 ---

## Wizard

  - wizard > pfsense setup > general information
  - set hostname  `sg01-pfsense`
  - set domain  `local.lan`
  - set primary dns = LAN ip address 172.16.100.1/24
  - set and document new password
  - uncheck 2 boxes at bottom of page 4 of wizard
  - add traffic graphs and service status from dashboard `+`

---

## Firewall rules
  - firewall > rules > wan > address
   - pass, proto=any, source=any, dest=any
  - firewall > rules > lan > address
   - pass, proto=any, source=any, dest=any

  - disable nat:  firewall > nat > outbound > disable radial button

---

## Enable Unused Ports

  - Interfaces > assignments > add available Interfaces
    - select int and enable > save

## Check for double-gateway issues
  - system > routing
  - make sure there is not more than one gateway entry

---

# Client Networking

---

## Commands
 - nmtui
 - /etc/sysconfig/network-scripts

#### Example

  -   TYPE=Ethernet
  -   PROXY_METHOD=none
  -   BROWSER_ONLY=no
  -   BOOTPROTO=none
  -   DEFROUTE=yes
  -   IPV4_FAILURE_FATAL=no
  -   IPV6INIT=no
  -   IPV6_AUTOCONF=yes
  -   IPV6_DEFROUTE=yes
  -   IPV6_FAILURE_FATAL=no
  -   IPV6_ADDR_GEN_MODE=stable-privacy
  -   NAME=enp3
  -   UUID=3824e285-6b7b-4cb8-a822-7a4d3d58f45a
  -   DEVICE=enp0s31f6
  -   ONBOOT=yes
  -   IPADDR=172.16.100.100
  -   PREFIX=24
  -   GATEWAY=172.16.100.1
  -   DNS1=172.16.100.1
  -   DNS2=192.168.2.1



 - disable ipv6
   - net.ipv6.conf.all.disable_ipv6 = 1
   - net.ipv6.conf.default.disable_ipv6 = 1
   - net.ipv6.conf.lo.disable_ipv6 = 1
