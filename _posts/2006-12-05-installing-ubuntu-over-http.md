---
layout: post
title:  Installing Ubuntu over HTTP
date:   2006-12-05 17:05:50.0
tags:
 - Ubuntu
 - Debian
 - pxe
 - install
 - tftp
 - dhcp

categories: update
---

It is very easy to install a new machine with Ubuntu or any other Debian-based distro using the PXE boot loader and fetching the distro right from the network (or from a local mirror).

You will need a separate machine - likely another Ubuntu - to set up a TFTP server and to configure the boot option using a local DHCP daemon. If you have another DHCP daemon you have to turn it off, as a DHCP daemon supporting TFTP boot must be provided.

You can install both the DHCP daemon and the TFTP server on Ubuntu using the following commands:

    
    apt-get install tftpd-hpa
    apt-get install dhcp3-server


You will then have to fetch the correct Netboot image file from Ubuntu and update the configuration for both services.


* Downloading the Netboot image file
You can get the correct Netboot image file from e.g. [http://archive.ubuntu.com/ubuntu/dists/edgy/main/installer-i386/current/images/netboot/netboot.tar.gz](http://archive.ubuntu.com/ubuntu/dists/edgy/main/installer-i386/current/images/netboot/netboot.tar.gz). 
You should download the netboot file from the distro you want to install, so that it's already configured with the correct mirror addresses.


* Placing the Netboot image file in the TFTP directory
Run the following command to unpack your Netboot installer in the TFTP directory.
    
    tar zxvf netboot.tar.gz -C /var/lib/tftpboot



* Configuring your local DHCP server
Edit the file */etc/dhcp3/dhcpd.conf* to contain the following lines. I assume your subnet is 10.10.4.x and your DHCP server is 10.10.4.103:

    
    ddns-update-style none;
    option domain-name "mydomain.tld";
    option domain-name-servers 212.158.248.5;
    ping-check = 1;
    default-lease-time 600;
    max-lease-time 7200;
    log-facility local7;
    subnet 10.10.3.0 netmask 255.255.255.0 {
       range 10.10.3.200 10.10.3.210;
       option routers 10.10.3.1;
       filename="pxelinux.0";
       next-server 10.10.4.103;
    }


Make sure any other DHCP server on the LAN is turned off.


* Starting services
Restart the local DHCP service and the local TFTP service.

    
    /etc/init.d/tftpd-hpa restart
    /etc/init.d/dhcp3-server restart



* Booting the new machine
On the new computer, enable network boot.
Start it and it should "find" the new DHCP, download the image and boot with Ubuntu.
It will auto-configure itself and then ask for a mirror to download from.
Before you select the mirror, make sure you have your DSL connection working or it will just hang.


* Stopping TFTP and DHCP
When you are done installing, do not forget to stop both the TFTP and the DHCP servers.

    
    /etc/init.d/tftpd-hpa stop
    /etc/init.d/dhcp3-server stop





