+++
title = "cosmic-desktop"
date = "2026-01-08"

[taxonomies]
tags = ["arch", "desktop"]
+++

# cosmic-desktop install and experience
 ## 11:37
 so...ive grown a little tired of garuda on the desktop.  it was time to change things up a bit.  i made sure to keep my btrfs sdd current and copied all necessary /home files etc before taking the plunge.  i decided i was going to make it my only DE and so i needed to start completely over from scratch.  some things are easier than others and i found that i'll need some work arounds to get things working with my server connections etc. booting it up will tell me alot.  

so i started with the most recent arch iso, pulled from their repo, i found the below to download at about 10Mb/s which took like 4 min,  not bad for an iso mirror.
<https://losangeles.mirror.pkgbuild.com/iso/2026.01.01/archlinux-2026.01.01-x86_64.iso>

i used gnome-disk-utility to copy the iso to the USB and make it bootable...just restore use the restore function and select  the .iso file and it'll take a couple minutes.

i booted the OS into my bios, setup the USB boot first, and restarted. the MSI board i have also has a boot disk menu, i just wanted to make sure secure boot was off.

on boot, the desktop, which iis pretty fast, got to prompt in about 30s.  as I always do, i typed in `archinstall --advanced`

i went through the usual items, setup Partitioning to recommended layout and used btrfs here, and also left off the 7tb ssd so files wouldn't be overwritten.  in the type menu, i selected cosmic and made sure to setup cosmic-greeter as the 'login manager'.  no additional packages as i wanted to see what would come installed.  i also selected nvidia since my 4070ti was detected.  i used the new 'open' drivers 590.xx which is the general direction we are moving with that stuff.

after setting up password, user and static IP, i was ready to boot.  total install time from prompt was about 5 min to done.  reboot option selected and away we go. cosmic-greeter was nice, and set to default background both monitors came up and i entered my password i setup.  i was greeted by an informational bubble to walk me through some cosmic nuances.  i just clicked through it as i would rather just poke around.  some main differences off the bat:

- terminal was not fish - ill change that later
- a little better notification panel out of the box over gnome (what i usually use)
- some nuances in the always-on dock (adjustable to hide)
- panel usability is a little different, not sure if ill be able to add things, like my tailscale button in the settings panel as those are all individualized
- the dock has some cosmic configurable buttons that are specific to cosmic, workspace switching, etc

some similarities:
- has a gnome (feel) to it for sure
- upper panel pretty much the same with layout, perhaps a little more control in the cosmic-tweak function
- the super button brings up the application folder, with a tabbed layout rather than scroll (grouping function seems easier), but very familiar
- easier theming than gnome through settings > appearance and cosmic-tweaks

overall a little nuanced but familiar feel.  the animations are snappy and the details are nice. opted for a darker them with rounded windows and contrasting 'current window' highlights.

things i've tested out so far:
- virtualbox - same experience as garuda, causes fans to ramp even with low load in vm.  ive since setup virt-manager and have been very pleased with loads dropping to near idle with vm running (seeing 1% CPU load with virtmanager, rather than 4-7% with vbox) - HUGE difference. and i'll detail that experience in another post. 
- remmina - pretty much abandoned.  was having vnc issues with virt-manager, just ended up installing rustdesk in windows and on my arch garuda laptop, works just as well maybe even a little better, working on a rustdesk-server OSS implementation on my /homelab now.  will detail that more later
- apps:  i dont use office or anything, so i cant speak to that, but pretty much all arch packages are installed.
- chaotic AUR: setup was pretty much the same as garuda, though not included
- system: i have a few things to do still, fstab edit for nfs mounts.  i did move my /home files to the new dir from my 7tb ssd and reformatted it and added it to the new /home dir (same as last time). ive detailed that below, cause i forgot how i did it last time.

btrfs stuff:
```
sudo mkfs.btrfs -f /dev/sda1
sudo btrfs device add -f /dev/sda /home #this will basically do the same thing
sudo btrfs balance start /home
```

i redid all the git mounts to my forgejo server and setup obsidian to folder (notes).
i also installed yay and use it for building trivalent-bin, which is a FOSS browser (chromium-based) hardened and borrowing heavily from graphene OS vanadium browser and originally built for fedora secureBLUE.  try it out if you have a chance.  way better than Brave broswer.

that's it for now! so far smooth sailing and i might even redo the laptop at some point.  the virt-manager and rustdesk solutions were probably the most difficult part of getting back to where I was.  overall i had probably a 1/2 doing setup.