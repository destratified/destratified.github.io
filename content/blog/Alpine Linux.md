+++
title = "Alpine Linux Btrfs OpenRC Plasma install"
date = "2026-09-06"

[taxonomies]
tags = ["desktop", "alpine linux", "openrc", "btrfs"]
+++

# Alpine Linux 
 ## 19:06

in pursuit of trying to find another distro (seems like i always am) its a matter of staying sans systemd. and i tried doing alpine on my laptop before, but i was having touchpad driver issues, so i abandoned it for #gentoo. i was using gentoo on the NUC as my desktop as well, but i think that was the first install where i got gentoo working an it was just a regular old ext4 (included timeshift for backing up). i didn't love that configuration so i figured what the hell, lets try alpine.  

i went through 2 or maybe 3 installs till i felt comfortable with the commands and the flow of the install. #alpine is about as bare-bones as you get. reminds me of arch with archinstall. so as it got easier i started to dig into how best to go about it.

the simplest was to just use the alpine live environment, go figure! the btrfs instructions are excellent, as are all of Alpine's docs actually.  

make your USB, config the bootmanager if need be to boot from the USB and you'll be met with a very fast boot and an intimidating prompt.

sign in with "root", no password.
here's where i found it easiest to setup a few env variables and add a program or two, i would be ssh-ing in after boot, so i wanted the following:

```shell
apk add kitty btrfs-progs  
```

set a environmental variable before you go into setup will streamline things a bit:

```shell
export ROOTFS=btrfs
```

as outlined in the docs, [https://wiki.alpinelinux.org/wiki/Btrfs?__goaway_challenge=cookie&__goaway_id=ff47e4359cf9f396e0258c979c7850de&__goaway_referer=https%3A%2F%2Fwiki.alpinelinux.org%2Fw%2Findex.php%3Fsearch%3Dbtrfs%2Binstall%26title%3DSpecial%253ASearch%26wprov%3Dacrw1_-1](alpine btrfs)

this allows seamless setup of the btrfs filesystem as much as we need it for apk-snap, grub-btrfs and all the goodies.

enter the setup with `setup-alpine` i liked the flow of the installer, you can pretty much rip through all of the prompts. i set my own IP address in the network section, told it which disk i wanted the root partition on and you will see it make the btrfs autmoatically and install everything and you are ready to reboot.

boot to prompt, enter root or username and passwd and you are again left with a prompt. the next thing i wanted to do was get the system repos updated and install the apk-snapper `apk-snap` program to start leaving my self breadcrumbs in case i fucked something up. you'll need a couple more apps:

```shell
doas apk update && doas apk upgrade
doas apk add apk-snap nano
```

this will at least get snapshots going, i setup grub btrfs after plasma last time, but it could happen anytime really, i just needed the browser to be able to troubleshoot some issues. so the next step was `doas setup-desktop`. alpine really excels at its simplicity with everything here...i selected plasma and let it rip.

reboot once that's complete and you'll be met with a prompt for logging in on kde plasma.  

after in the desktop, i found my sound was working, bluetooth was off, networkmanager was disabled but internet was working so i could still continue without too much fuss. 

first order of business was changing repos to edge and adding testing. edit /etc/apk/repositories:

```shell
#/media/sdb/apks
http://mirrors.edge.kernel.org/alpine/edge/main
http://mirrors.edge.kernel.org/alpine/edge/community
http://mirrors.edge.kernel.org/alpine/edge/testing
```

then do a simple update to get to the edge (testing) platform and install the edge kernel also:

```shell
doas apk upgrade -U #same as the apk update & apk upgrade combined
doas apk add linux-edge #pulls linux-stable kernel
```

testing allows you to download librewolf for example.

i also found that i needed to change shells after alpine login as i use bash on all my other machines and have dot files that i store in a git repo and git clone with every machine setup. 

first install bash, and change user shell (root if you like), add a couple other essentials for editing files, term etc
```shell
apk add bash shadow kitty nano
doas chsh joe /bin/bash
#doas chsh root /bin/bash
```

verify the changes took place, this is easily found in the passwd file:
```shell
cat /etc/passwd | grep joe && cat /etc/passwd | grep root
joe:x:1000:1000:joe:/home/joe:/bin/bash
root:x:0:0:root:/root:/bin/bash
```

also, create a ~/.bash_profile you need for ssh logins, etc
```shell
# Load the interactive Bash configuration for login shells.
if [ -f "$HOME/.bashrc" ]; then
    . "$HOME/.bashrc"
fi
```

this way the ssh login will check for .bashrc and load it on access.

you essentially have the tools to continue, i like loading kitty term for alpine early because i am usually ssh-ing in off a kitty terminal on another machine and this will eliminate editing errors, i like nano for a simple editor, but use what you like...

