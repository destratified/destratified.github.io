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

this looked promising because it was built