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
sudo nano 
```
