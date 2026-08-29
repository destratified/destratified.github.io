+++
title = "fedora-snapper-grub-btrfs"
date = "2026-08-29"

[taxonomies]
tags = ["fedora", "grub-btrfs", "snapper", "rawhide"]
+++

# fedora-snapper-grub-btrfs 
 ## 15:44

this is my trimmed down nitty-gritty version of howto get fedora-rawhide with snapper, grub-btrfs working together

fedora has some quirks, some of which start out with preparing the proper btrfs snapshot partitioning early in the install process, the bullet list is as follows:

boot fedora44 live cd
create partitions in anaconda using the 
proceed back to anaconda, enter partitions manually
agree and install

this gives you the basic layout (foundation) for the snapper and grub-btrfs. 

to get snapper working:
install snapper and iotools-notify (btrfs-assistant helpful as GUI)
setup root and home snapper configs
take manual snapshots
confirm with snapper list

add grub-btrfs:
install grub btrfs with fedora flavoring
