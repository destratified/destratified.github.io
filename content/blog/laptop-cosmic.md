+++
title = "laptop_cosmic"
date = "2026-01-10"

[taxonomies]
tags = ["laptop", "arch", "cosmic"]
+++

# laptop cosmic installation
 ## 17:26
 since the last time i'd attempted to install arch on my laptop, i opted for the garuda arch variation because it took figuring out the iwctl protocol off the table and just worked.  well, i found garuda kinda bloaty and many things i didnt need, so i though i'd test out the cosmic desktop experience.  i've used it for a while on my desktop and now it was time to try installing it on my laptop.  plus, its now out of alpha and is included in the archinstall desktops options.
## so what is new?
the archinstall included the wifi setup at initial prompt, its buggy, kept closing...then working, but saying it didn't, after a couple trys, on the third archinstall command, the installer started pulling repos, indicating it actually had worked. archinsttall itself was pretty much the same, just selected cosmic in the type menu and left pretty much barebones.

it installed in about 3 min and was ready to reboot.  startup was un-eventful, but started with a few buggy issues.  going through the initial setup, any prompt asking for the admin password was unresponsive.  i pulled up a terminal `Super + T` and a quick interrnet search on my phone indicated that it was a polkit issue, and the remedy was a quick chmod command...im sure that will get ironed out soo, easy fix either way:

```
sudo chmod 4755 /usr/lib/polkit-1/polkit-agent-helper-1
```
and back in business.  this assisted me in setting up nearest city for time settings.

installer assisted with the internet connection as well (i chose network manager)

## headaches
so i had a bitch of a time installing all my  usual shit and after having the laptop mostly working, yay or paru would cause a change in required files by pacman and tanked my reflector command operation and fucked up my rustdesk and trivalent pulls from chaotic AUR.

workarounds:
- rustdesk: download the pkg file
- trivalent: build direct from git
- dont install yay or paru (probably shouldnt anyways)
- forgejo mirror to my github repo stopped pushing (403 error) - likely unrelated

### rustdeck:

i've tried teamviewer, remmina and was having no love connecting to my win11 vm remotely via any of them.  teamviewer and remmina were buggy and slow, and i ran across rustdeck online and thought id give it a try.  at first go it worked flawlessly connecting out to the win11 vm.  i was able to setup permanent passwords.  it wouldn't connect to the laptop running wayland from win11, they were pretty clear it was still experimental, but it dd connect to windows, which is what i needed anyways. there was a ready indication for the connection at the bottom which recommended self-hosting.  i tried the rustdesk option for the server creating my own lxc w? debian 13 on my homelab and installing with the script:

```
wget https://raw.githubusercontent.com/techahold/rustdeskinstall/master/install.sh
sudo chmod +x install.sh
sudo ./install.sh
```

this didnt work at all for me, the webpage wasn't showing any useable info, couldnt get the terminals to connect.  i did notice a small blurb during the rustdesk server install that said that there was an alternative FOSS version put out by <https://github.com/infiniteremote/installer> 

the README.md states:
```
### Why Infinite Remote?

[](https://github.com/infiniteremote/installer?tab=readme-ov-file#why-infinite-remote)

We have started our own project due to the lack of transparency of the RustDesk project and intend on forking and maintaining our own servers and clients, the vulnerabilities in the RustDesk client is too much for business use so we will be working hard on improving the codebase and security.
```

this install went fine.  the webportal signin brought me to a more usable and understandable interface, the copy links didn't work in the installer page, but the important parts, API webpage and key were easy to see and copy.  the portal indicated the logins from each instance, with relevant details and the api login worked ok on the win11, i could use the addressbook showing the opensource api was working great! what a nice free add-on.

### trivalent - secureBLUE browser
a pretty long history of being a brave browser user.  the gimmicky shit with rewards etc were annoying and the founder was a shithead so i started looking for alternatives.  i wanted to keep with chrome (albeit a hardened version) and mainly for use of extensions - i like functionality of bit-warden since i have a self-hosted instance, and privacy badger doesnt hurt either.  i wish there was a true graphene vanadium port for linux, but the secureBLUE chromium port from Fedora is hardened and uses vanadium patches for additional tightening.  you will have to enable extensions from settings and all pop-ups blocked automagically.  here's the code for the install from git:

```
```