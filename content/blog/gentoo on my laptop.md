+++
title = "gentoo on my laptop"
date = "2026-04-25"

[taxonomies]
tags = ["gentoo", "openrc"]
+++

# gentoo on my laptop 
 ## 08:58
it's not surprising how i got here i guess...after running debian forky (14) on my laptop with openrc for over a month and really liking it, i thought to myself - since openRC is a gentoo native product, how hard could it be getting gentoo to install on the laptop??

well, that's harder than you think. i could tell from running the openrc live cd that everything would function, but since the gentoo install is way more manual, and difficult, the 2-day+ process began. 

now after sitting down and forcing my way through all the setup, then the tweaks, i've got myself a usable DD that has everything my openRC deb install does, with less glitches in the boot system (i've had to build my own openRC init files on deb, and gentoo's were provided, natively).

here's what i have left to do, nfs access to local file server, when at home (shouldn't be too bad), and getting my librewolf / proxmox browser configuration to work (doesn't like the vnc aspect), but that's a browser issue, and i can ssh in anyway, which is usually better. i will also eventually setup keyd, as its not hard, but my pesky delete button still doesn't work and I FUCKING HATE THE CAPLOCKS BUTTON <-- see what i did there?

### gentoo install:
- difficult
- time-consuming
- learing new commands and how system functions
- USE flags, holy shit
- stable and custom built
- fast
- openRC natively
- has a handbook, follow it to a "t"

#### starting off
____________________
follow the handbook, until you get a feel for how this is gunna go, i setup and wiped out 3 installations before i felt i had a handle on it, and not in VMs. if you have installed arch from the commandline, and NO, not with archinstall, this will seem very familiar. i started with the liveCD and after i understood how it was working, ended up using my running debOS to install via changeroot.

things that helped me...if you have an entirely separate drive install it there - i went with my second nvme drive and created 3 partitions. i used 10GB for efi, 20GB for swap and 100GB for the / - ext4 i have not had the pleasure of installing btrfs with gentoo or openRC debian, and i may not. i was using it for snapshots, but timeshift, which works in debian and gentoo works with the ext4 partitions and i've been able to restore using it - *with access to the system*

keep the efi boot partitions separate, you can update-grub to each individually and with os-prober it will pickup the other distro. this is easier and more safe than writing two different installs to the same partition...if i change anything there, it would be to use refind or limine and have it chain boot.

once partitioned, you can use mkfs to format the partitions with the appropriate types "efi,swap and ext4", in my case, and then mount.  gentoo handbook directions state to mkdir and mount to /mnt/gentoo initially, i found after that and the /proc /dev etc that you can also mount the efi to /mnt/gentoo/boot to save a little time later and keep with the flow.

at this point you can changeroot and follow the handbook and get the system to a booting state.

#### reboot and start installing stuff
________
instead of trying to do everything at once, i found it was best to get to a point where i had a boot and prompt with root login. there are a few guides out there for kde installs that will have you setup kde, user and such before initial boot. i liked the gentoo handbook way and after a solid reboot sort of start the install process.  i have binpackages enabled, which helps cut some install time down, but mostly i found that i needed to setup some global flags.

here is my /etc/portage/make.conf:
```shell
# These settings were set by the catalyst build script that automatically
# built this stage.
# Compiler flags to set for all languages
COMMON_FLAGS="-march=native -O2 -pipe"
# Use the same settings for both variables
CFLAGS="${COMMON_FLAGS}"
CXXFLAGS="${COMMON_FLAGS}"

#Rust flags
RUSTFLAGS="${RUSTFLAGS} -C target-cpu=native"

# Appending getbinpkg to the list of values within the FEATURES variable
FEATURES="${FEATURES} getbinpkg"
# Require signatures
FEATURES="${FEATURES} binpkg-request-signature"

# This sets the language of build output to English.
# Please keep this setting intact when reporting bugs.
LC_MESSAGES=C.UTF-8

#USE flags
USE="-gtk -gnome -systemd"

#USE flags KDE
USE="X wayland bluetooth elogind"

#Networks/bluetooth
USE="bluetooth"
USE="${USE} networkmanager"

#Use flags printer
USE="cups foomaticdb ppds usb"

# Overrides the profile's ACCEPT_LICENSE default value
ACCEPT_LICENSE="* @FREE @BINARY-REDISTRIBUTABLE @GPL-COMPATIBLE"

#grub install directions
GRUB_PLATFORMS="efi-64"

#keyword
ACCEPT_KEYWORDS="~amd64"

#automatically use bin packages first
EMERGE_DEFAULT_OPTS="${EMERGE_DEFAULT_OPTS} --getbinpkg"

#Global set video 
VIDEO_CARDS="intel"
```

there's probably a few things here that could be consolidated, 