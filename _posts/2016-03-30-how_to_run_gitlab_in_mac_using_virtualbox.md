---
layout: post
title: "How to run Gitlab Server in Mac OSX using Virtualbox"
date: 2016-03-29
author: mingxiang
---

### How to run Gitlab Server in Mac OSX using Virtualbox

1. Download [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
2. Download [Ubuntu 14.04](http://www.ubuntu.com/download/server)
  - recommended version for gitlab is 14.04
  - recommend using server version so less performance hit from graphics rendering on VM
3. Create VM in VirtualBox
  - OS Type: Linux
  - OS Version: Ubuntu 64-Bit
  - RAM: 1.5GB
  - Virtual Hard Disk: Pick other types if transferring between VMs, Dynamically Allocated, 8GB
4. Select created VM -> Settings -> Network -> Adapter 1
  - change `Attached to:` from `NAT` to `Bridged Adapter`
5. Start created VM, go through Ubuntu Installation process
6. After installation completed:

  **Set up static IP**

  Get IP_ADDRESS,NET_MASK, and GATEWAY
  ```
  ifconfig -a
  ```

  Find the values from under eth0

  IP_ADDRESS = inetaddr (e.g. 192.168.1.*)

  NET_MASK = mask (e.g. 255.255.255.0)

  ```
  route -n
  ```

  GATEWAY = eth0 row, Gateway IP column

7. Update system network config

  `sudo vim /etc/network/interfaces`

  Update it to look like

  ```
  auto eth0
  iface eth0 inet static
  address <IP_ADDRESS>
  netmask <NET_MASK>
  gateway <GATEWAY>
  ```
8. Install Gitlab and configure

  Recommended to install from [Gitlab CE Omnibus](https://about.gitlab.com/downloads/)

  ```
  sudo apt-get install curl openssh-server ca-certificates
  curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
  sudo apt-get install gitlab-ce
  sudo gitlab-ctl reconfigure
  ```

  Update Gitlab configuration to point to static IP

  `sudo vim /etc/gitlab/gitlab.rb`

  Update to `external_url "http://<IP_ADDRESS>"`

  Then reconfigure Gitlab to use new external url and restart

  ```
  sudo gitlab-ctl reconfigure
  sudo shutdown -r now
  ```
9. You can now access the Gitlab server from your host machine using the IP you configure
  `http://<IP_ADDRESS>`

10. Optional: You can further configure the host file on your host machine to access your Gitlab server through a named address

  ```
  sudo vim /etc/hosts
  ```

  Update it with a new entry for Gitlab

  ```
  <IP_ADDRESS> <your_preferred_name>
  ```

  e.g.

  `192.168.1.133 local_gitlab`




