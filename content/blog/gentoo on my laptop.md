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
follow the handbook, until you get a feel for how this is gunna go, i setup and wiped out 3 installations before i felt i had a handle on it. if you have installed arch from the commandline, and NO, not with archinstall, this will seem very familiar. i started with the liveCD and after i understood how it was working, ended up using my running debOS to install via changeroot.

things that helped me...if you have an entirely separate drive install it there - i went with my second nvme drive and created 3 partitions. i used 10GB for efi, 20GB for swap and 100GB for the / - ext4 I have not had the pleasure of installing btrfs with gentoo or openRC debian, and i may not. i was using it for snapshots, but timeshift, which works in debian and gentoo works with the 