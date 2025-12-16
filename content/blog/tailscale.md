+++
title = "some useful shit you can do with tailscale"
date = "2025-08-29"
+++



# RDP
so I needed to do this for work, the idea being that I had to have access to a win10+ program on my laptop for work that has Linux arch.

I don't own windows so the gist is this:
1. install virtual box for arch
2. download and install win10+
3. debloat windows
4. enable rdp on win, setup new user and make sure rdp settings are ready
5. install and connect tailscale in win
6. install remmina and freerdp
7. turn on tailscale in laptop and connect to tailscale of rdp for win

caveats:  with this method i get a larger fullscreen with rdp, but if like today, windows updated overnight and without having logged back in, i lost my tailscale connection.  I am now trying the rdp function for virtualbox over my desktop tailscale connection, and that should help so i can login as long as the vm is up

# Gnome notifications and menu toggle button
most of the nitty gritty is written up in the discussion on github here:
https://github.com/StorageB/custom-command-toggle/discussions/18#discussioncomment-14591824
add the extension below to gnome using the extensions menu in chrome/brave
command-custom-toggle and install then go into settings and follow the below:

i was looking for for a solution for any easy start/stop option for the custom tailscale bash script i've setup for connecting to my home server while away.  i use the home server as an exit route which helped with the above stuff as well, and use routes for accessing everything on a certain lan at home
