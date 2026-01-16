+++
title = "keyd"
date = "2026-01-16"

[taxonomies]
tags = ["arch", "keyboard_remapping"]
+++

# keyd 
 ## 08:31
so i definately started over here...forgot entirely how i modded the keymappings for my laptop. the lemur13 from system76 is a smaller 13" laptop and thus a small keyboard. in addition to a few smaller keys, the caplock i never use and hit often. and the page up/page down are both 1/2 key above the arrows. objectives:

map caplocks to shift
map page up/ page down to left/right
map left ctrl + page up/page down to page up/page down

first theres a few commands to pull the github repo and install thanks to [Kari Martilla Blog](https://www.karimarttila.fi/keyboard/2023/11/16/linux-keyboard-configuration-with-keyd.html)
```
git clone https://github.com/rvaiya/keyd
cd keyd
make && sudo make install
sudo systemctl enable keyd && sudo systemctl start keyd
```

this gets everything loaded. i remember last time that i used `sudo keyd monitor` to get all the keystroke names sorted for the keys i wanted to map, left control, pageup pagedown, left right etc so i could begin adding to the configuration file and testing the changes. i also had this on the desktop clackity keyboard since i kept fat fingering the capslock - at least there it gave me an led indicatior capslock was on ;)

```
sudo nano /etc/keyd/default.conf
```

and here's my final config...who the fuck uses capslock anyways???

```
# NOTE: The panic sequence *<backspace>+<escape>+<enter>* will force keyd to terminate.

### NOTE:
# This file needs to be in:
# /etc/keyd/default.conf
# If you are reading the ~/info/default.conf file, it is a backup file.
# NOTE: Remember to add changes in /etc/keyd to git!

### HELP SECTION BEGIN ###

# Web site: https://github.com/rvaiya/keyd
# See also Finnish keyboard layout:
# https://www.vero.fi/en/About-us/contact-us/efil/information-on-mytax/less-commonly-used-latin-characters-on-a-finnish-pc-keyboard/

##### KEYD #################
# When you edit /etc/keyd/default.conf file:
# cd /etc/keyd
# cn # Open VSCode
# In another terminal:
# sudo journalctl -eu keyd -f
# ... this way you immediately see if there is some error,
# when you load new configuration (after saving the file):
# sudo keyd reload
# Finally when you are ready, in the /etc/keyd: add changes to git.

# Find keys with tool:
# sudo keyd monitor

# List keys that you can use with keyd:
# sudo keyd list-keys

### HELP SECTION END ###


### TODO BEGIN ###

# No issues at the moment.

### TODO END ###


###### KEYBOARD IDENTIFICATIONS #####
# NOTE: Later on if you find out that some configuration does not work on Dygma vs Laptop keyboard,
# you can use separate ids sections.
[ids]

*


###### MAIN #####
[main]
# Using sudo keyd monitor:

#remap pageup/pagedown to left/right
pageup = left
pagedown = right

#remap capslock to Left Shift
capslock = leftshift

#with rightcontrol pageup/down return to normal
rightcontrol = layer(returnpageupdown)

[returnpageupdown]
pageup = pageup
pagedown = pagedown
```

this does exactly what i needed