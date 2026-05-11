+++
title = "gentoo and btrfs"
date = "2026-05-07"

[taxonomies]
tags = ["gentoo", "btrfs"]
+++

# gentoo and btrfs 
 ## 10:03

well, the gentoo bug has bit me i suppose. i've got gentoo on a ext4 setup on my laptop and its working great, used timeshift with rsync to create a reliable and usable backup and restore. tested and works great as long as you can boot into it. now, i had my desktop miniPC a NitroPC with open bootloader and had been running debian with openRC on it, but kept coming back to the idea if i wanna run openRC, why not just go to the source. gentoo is well integrated and having to adapt an build my own init files was fun, but a little annoying. its baked in, and just works.

so i set about finding a couple guides, used the handbook where it makes sense, and seemed after some arduous trial and error got it functioning. so the first guide i used, which got me to a boot prompt was <https://gist.github.com/xmawja/da6a7ee664271631ae5cd4de0dc4f249>

in this guide, everything worked for the most part, but i found i was having trouble updating grub when a new kernel was loaded - couldnt properly find boot partition and i was only able to boot from an efi stub that i'd copied and not from grub.

this issue i think was in the two partitions that were being setup in the guide, so after i was in my WM - id gone as far as to setup kde, i decided that in order to get dracut, intiramfs and mkconfig-grub to all play nice together i needed to redo the boot partition.  

this was not for the faint of heart, as i had to wipe it from the liveCD and chroot in...which is a pain in the butt with a btrfs setup as you have many partitions to load.  i was able to shortcut this with the guide a bit as alot of the mount commands were copy and paste but it kinda went like this:

1) using partition-manager in gentoo liveCD, delete the /boot (/dev/sda1 - ext4) and /boot/efi (/dev/sda-2 EFI) partitions - this leave swap (if you need it) and the btrfs partition with / and others - apply changes

2) following the guide principles create one new partition for boot, i used 10GB and set it as fat32. apply changes, then select the partition (right-click) and toggle boot flags (make sure partition is not mounted) and apply changes

3) when you follow the guide above, this will give you 3 partitions - /dev/sda1 (EFI - fat32), /dev/sda2 (swap) and /dev/sda3 (root - btrfs). you can then continue along with the guide and just be mindful of the mounting locations later and the fstab entries.
```shell
# mount root
mount -o defaults,noatime,space_cache=v2,compress-force=zstd,autodefrag,subvol=@ /dev/sda4 /mnt/gentoo

# Create BOOT and other Directory to be mount on boot partion|
# create desire directorys to be mountes as a SubVolume|
mkdir -p /mnt/gentoo/{boot,home,opt,srv,tmp,usr,var,vms,.snapshots}

# mount other subvolumes|
mount -o defaults,noatime,space_cache=v2,compress-force=zstd,autodefrag,subvol=@home /dev/sda4 /mnt/gentoo/home
mount -o defaults,noatime,space_cache=v2,compress-force=zstd,autodefrag,subvol=@opt /dev/sda4 /mnt/gentoo/opt
mount -o defaults,noatime,space_cache=v2,compress-force=zstd,autodefrag,subvol=@srv /dev/sda4 /mnt/gentoo/srv
mount -o defaults,noatime,space_cache=v2,compress-force=zstd,autodefrag,subvol=@tmp /dev/sda4 /mnt/gentoo/tmp
mount -o defaults,noatime,space_cache=v2,compress-force=zstd,autodefrag,subvol=@usr /dev/sda4 /mnt/gentoo/usr
mount -o defaults,noatime,space_cache=v2,compress-force=zstd,autodefrag,subvol=@var /dev/sda4 /mnt/gentoo/var
mount -o defaults,noatime,space_cache=v2,compress-force=zstd,autodefrag,subvol=@vms /dev/sda4 /mnt/gentoo/vms
mount -o defaults,noatime,space_cache=v2,compress-force=zstd,autodefrag,subvol=@snapshots /dev/sda4 /mnt/gentoo/.snapshots
```

use whatever your drive partitions are called (mine were nvme0n1). i did not reinstall the tarball, so i the /boot partition on root was empty at this point after everything was unmounted and deleted.

at this point you can just mount stuff and chroot in:
```shell
# Copy DNS info
cp --dereference /etc/resolv.conf /mnt/gentoo/etc/


# Mounting the necessary filesystems
mount --types proc /proc /mnt/gentoo/proc
mount --rbind /sys /mnt/gentoo/sys
mount --make-rslave /mnt/gentoo/sys
mount --rbind /dev /mnt/gentoo/dev
mount --make-rslave /mnt/gentoo/dev
mount --rbind /run /mnt/gentoo/run
mount --make-rslave /mnt/gentoo/run

# Entering the new environment
# chroot
#env -i HOME=@ TERM=linux chroot . bash -l|
chroot /mnt/gentoo /bin/bash

# Source ENV
source /etc/profile

# Change Terminal PS1 Name
export PS1="(chroot) ${PS1}"
```

after the chroot i was able to follow the gentoo handbook a bit regarding the /efi partition.

```shell
mount /dev/sda1 /efi
```

now to make sure dracut, initramfs and all that shit points at the right place, i re-installed my kernel...
```shell
emerge -avU gentoo-kernel-bin
```

and let things rebuild, it did pickup the /efi location this time.

then i did grub-install
```shell
grub-install --efi-directory=/efi
```

and no errors were showing...so kept moving along in the guide with the applicable parts.

```shell
grub-mkconfig -o /boot/grub/grub.cfg
```

this was the moment of truth! now all output showed the efi detected, kernel found, and snapshots! another thing that i think was helpful was i had installed the grub-btrfs program before i wiped everything out and started over. that was the utility that picked up the snapshots. grub-btrfs has its own config file, so take a look at that with `nano /etc/default/grub-btrfs/config` and tweak anything you lost, or preferred working, # of snapshots in the list etc.

i also wiped out the pretty boot screen and had to find a way to get that back.  fortunately its on github...
<https://github.com/Telemin/gentoo-grub-themes>

so go ahead and reboot back into the machine and then you can fuck with grub, also check out the new snapshot menu item if you have grub-btrfs installed.

to get the gentoo glass theme back, you need to figure out the proper directory from the config file...mine only had starfield (which is not as nice) and so i had to make the directory /boot/grub/themes/glass and then i cloned the full repo from the link above and `cp -r /gentoo-glass/ /boot/grub/themes/glass/` from the cloned directory

verify that the files were in the right place, and change the line for the grub theme location in /etc/default/grub to 
```shell
GRUB_THEME="/boot/grub/themes/glass/theme.txt"
```

or whatever your directory is named, and voila a pretty bootsplash menu and working btrfs, timeshift, snapshots, all in grub!

and a small update...i have torched my gentoo a couple of times now. and i have used timeshift to restore it as well. now, in one dumb blow i accidentally removed my 7.0.5 kernel image, and purposefully tampered with modemmanager and was left with a flashing boot screen at prompt. thats new to me. thankfully i have a debian openRC install as backup on the laptop that has timeshift installed. according to the website, all you have to do is tell it where to find the rsync backups and you can point it to other partitions! i showed it my timeshift partition, selected the root partition and told it where the bootloader was to be installed and after some acknowledgements it worked, i was able to get back into my snapshot before i fucked everything up.