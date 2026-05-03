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
# ------------------------------------------------------------
#  Compiler flags (all languages)
# ------------------------------------------------------------
COMMON_FLAGS="-march=alderlake -mabm -mno-kl -mno-pconfig -mno-sgx -mno-widekl -mshstk --param=l1-cache-line-size=64 --param=l1-cache-size=32 --param=l2-cache-size=12288 -O2 -pipe"
CFLAGS="${COMMON_FLAGS}"
CXXFLAGS="${COMMON_FLAGS}"
RUSTFLAGS="${RUSTFLAGS} -C target-cpu=alderlake"

# ------------------------------------------------------------
#  Locale (English messages)
# ------------------------------------------------------------
LC_MESSAGES="C.UTF-8"

# ------------------------------------------------------------
#  Global USE flags
# ------------------------------------------------------------
USE="wayland bluetooth elogind networkmanager nfs ext4 -gtk -gnome -systemd cups foomaticdb ppds usb"

# ------------------------------------------------------------
#  Portage FEATURES (single line, no self‑reference)
# ------------------------------------------------------------
FEATURES="getbinpkg binpkg-request-signature ccache"

# ------------------------------------------------------------
#  License handling
# ------------------------------------------------------------
ACCEPT_LICENSE="* @FREE @BINARY-REDISTRIBUTABLE @GPL-COMPATIBLE"

# ------------------------------------------------------------
#  Bootloader configuration
# ------------------------------------------------------------
GRUB_PLATFORMS="efi-64"

# ------------------------------------------------------------
#  Architecture / keyword selection
# ------------------------------------------------------------
ACCEPT_KEYWORDS="~amd64"

# ------------------------------------------------------------
#  Emerge default options (no duplicate --getbinpkg)
# ------------------------------------------------------------
EMERGE_DEFAULT_OPTS=""

# ------------------------------------------------------------
#  Video driver selection
# ------------------------------------------------------------
VIDEO_CARDS="intel"

# ------------------------------------------------------------
#  Parallel builds & ccache location
# ------------------------------------------------------------
# Replace “8” with the number of CPU threads you actually have.
# If you have less RAM, lower the “-l” value (e.g., -l4).
MAKEOPTS="-j10 -l12"
CCACHE_DIR="/var/cache/ccache"
```

there's probably a few things here that could be consolidated, but overall its pretty neat. i've got 50 some packages from world and total 1180, so pretty lean for a DM.

the guru package and nerd-fonts, are helpful, timeshift using rsync for ext4...i might play with a btrfs and timeshift install next, but thats probably for the desktop. i've rescued the system a few times already, the latest when i did something to my package manager where it would only pull the same gentoo repo over and over...sometimes easier to just go back and start over.

i'd recommend timeshift for pretty much any install at this point, even vanilla ones.