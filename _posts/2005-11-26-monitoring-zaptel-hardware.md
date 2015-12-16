---
layout: post
title:  Monitoring Zaptel Hardware
date:   2005-11-26 09:33:58.0
tags:
 - #frontpage
 - zaptel
 - pci
 - linux
 - hardware

categories: update
---

When installing Zaptel hardware (or other Asterisk-related hardware) in Linux, it is often worth checking its status from the command line.

The following commands are useful to make sure that everything is working fine.

**lsmod**

This command lists loaded modules in the kernel. You should expect to find the generic *zaptel* module and then one hardware-driver for it, like *wct4xxp*.

**lspci -s**

This command lists the boards attached to the PCI bus of the local machine. Its output may get quite long. 
It is possible that your PCI adaptor is not decoded perfectly in its name, but it will surely be shown with its IRQ and its associated flags.

**cat /proc/interrupts**

This shows interrupt (i.e., requests for service) that your board has asked to the CPU. Make sure your boards are on an interrupt by themselves.

**cat /proc/zaptel/1**

By issuing this command, you'll see the status of all defined channels for span 1 of your cards. If you have more than one span (or more than one card) you can use *cat /proc/zaptel/2*, *cat /proc/zaptel/3*, etc.



