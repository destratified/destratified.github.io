+++
title = "laptop_cosmic"
date = "2026-01-10"

[taxonomies]
tags = ["laptop", "arch", "cosmic"]
+++

# laptop cosmic installation
 ## 17:26
 since the last time i'd attempted to install arch on my laptop, i opted for the garuda arch variation because it took figuring out the iwctl protocol off the table and just worked.  well, i found it kinda bloaty and many things i didnt need, so i though i'd test out the cosmic desktop experience.  used it for a while on my desktop and now it was time to try installing it again.  plus, its now out of alpha and is included in the archinstall desktops options.
## so what is new?
the archinstall included the wifi setup at initial prompt, its buggy, kept closing...then working, but saying it didn't, after a couple trys, on the third archinstall command, the installer started pulling repos, indicating it actually had worked. archinsttall itself was pretty much the same, just selected cosmic in the type menu and left pretty much barebones.

it installed in about 3 min and was ready to reboot.  startup was un-eventful, but started with a few buggy issues.  going through the initial setup, any prompt asking for the admin password was unresponsive.  i pulled up a terminal `Super + T` and a quick interrnet search on my phone indicated that it was a polkit issue, and the remedy was a quick chmod command...im sure that will get ironed out soo, easy fix either way:

```
sudo chmod 4755 /usr/lib/polkit-1/polkit-agent-helper-1
```
and back in business.  this assisted in setting up nearest city for time management.

installer assisted with the internet connection as well (i chose network manager)

## headaches
so i had a bitch of a time installing all my  usual shit and after having the laptop mostly working, yay or paru would cause a change in required files by pacman and tanked my reflector command operation and fucked up my rustdesk and trivalent pulls from chaotic AUR.

workarounds:
- rustdesk: download the pkg file
- trivalent: build direct from git
- dont install yay or paru (probably shouldnt anyways)
- forgejo mirror to my github repo stopped pushing (403 error) - likely unrelated