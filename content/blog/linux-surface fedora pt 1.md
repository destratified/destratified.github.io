+++
title = "linux-surface fedora pt 1"
date = "2026-08-05"

[taxonomies]
tags = ["surface pro9", "fedora"]
+++

# linux-surface fedora pt 1 
 ## 06:59

## Main installation instructions
_____________________
this is a brief overview of how i initially got fedora working on my linux-surface pro 9.

needs:
- fedora44 (or latest) live cd on bootable usb (i used KDE)
- USB-C to USB-A adapter
- USB-A keyboard and mouse plugged into USB-C adapter

using bootable live cd on usb and USB-C to USB-A adapter, boot into surface bios and make sure boot from usb is at the top of the boot list, also make sure secureboot is off and restart

this should bring up the fedora live cd boot screen. once inside click install to get going with the anaconda system installer, you can dual-boot with windows if you first make space by shrinking win partition, i said fuck windows and wiped whole disk
fedora has wonderful btrfs support out of the box with snapper (for snapshots) and grub-btrfs i recommend that for the install, but you do xfs, ext4 whatever.

got through the rest of the installation, and reboot

once rebooted, you can setup some things, i usually start with the grub-btrfs and snapper setup as i like to have snapshots available in case i ruin shit, which i often do.

its fairly easy compared to the setup i went through on my gentoo install, any way, now you can install linux-surface copr repo and add the linux-surface kernel goodies, which as of late was up to kernel 6.19

[https://github.com/linux-surface/linux-surface/tree/master/patches](linux-surface repo)

the fedora setup is pretty self-explanitory, following the directions here is a good start. 

[https://github.com/linux-surface/linux-surface/wiki/Installation-and-Setup#Fedora](fedora-instructions)

then make sure to pay attention to the directions specific for your model - i only have the surface pro 9

[https://github.com/linux-surface/linux-surface/wiki/Surface-Pro-9](surface-pro9 caveats)

reboot into linux-surface kernal and test things out!

this gets you into a fairly well-rounded stable install of fedora, which is most likely good for the vast majority of folks...however if you want to take things further, make sure to go through the steps to get secureboot working and make sure that's operating nicely and then proceed to pt 2, where i attempt to clone torvalds kernel source starting at linux-7.2-rc4 and modify patches to get them working with the surface.  