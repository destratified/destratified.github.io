+++
title = "keylightc - system76"
date = "2026-04-15"

[taxonomies]
tags = ["devops", "backlight"]
+++
# keylightc - system76 
 ## 14:13

so one thing that i missed after leaving systemd behind with my debian-openRC build was the keyboard backlight app that i was using, and it worked really well. i hadn't really looked into what it would take to get this going (since it had just worked), so i went down the rabbit hole again.

my internet searches came up with alot of nothing, very few openrc iterations, so i started looking for platform independent versions, and most of them were an iteration of c, or python.  eventually found a c program that handled input devices in a way that i thought was similar to mine, and so i forked Michael Marleys repo @ <https://gitlab.com/mamarley/keylightc.git> 

this looked promising because it was built for a modern framework laptop and my system76 is also opensource, and in checking the keylightc.c file, i was hoping that would be all i had to do...but nah, that didn't work at all.

for the record, i thought all i was going to have to change were the two lines referring to the keyboard and touchpads events and then the file that is used to change the brightness (learned that its talking directly with the kernel). 

my two devices are:
keyd virtual keyboard
ELAN0412:00 04F3:311D Touchpad

and the brightness level path is: /sys/class/leds/system76_acpi::kbd_backlight/brightness

it took a little bit of digging for the inputs, but i eventually found the easiest way was to install evtest and run `sudo evtest` and it would print out all the event devices and then you could select one and test for proper output so you would know for sure. i recommend that as the easiest method, theres also libinput and a few others...

the brightness path i sorted out with the brightnessctl program, you can install that and it will give you the various devices and output paths that work on your system. this gives you video and many more, but the key is the backlight. you can set the backlight directly with the command which is what the c program is doing.

there's also a fade function in the keylightc program that i wasn't sure was gunna work, so i pulled out all of the references, built the keylightc file with make and began testing it. `sudo ./keylightc` - no joy.

welp, i guess i don't know enough about c to get this working, so i piped the code into duck.ai

