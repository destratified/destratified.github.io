+++
title = "automatic-keyboard-backlight"
date = "2026-03-08"

[taxonomies]
tags = ["arch", "cachyos"]
+++

# automatic-keyboard-backight 
 ## 16:02

so i had a small issue. when i put cachyos, or arch on my system76 laptop, the system76-drivers and a few other system76 packages need built for the system. since i am running only cosmic, some of the gnome specific things don't work - like gnome-power-management and some others. i had under the advisement of system76 techs installed paru and then build packages for some better functionality.

these included system76-drivers, system76-firmware-git and system76-firmware manager at the bare minimum.

see <https://support.system76.com/articles/system76-driver/> for details and additional instructions.

AFAIK this adds some additional functionality, like airplane-mode toggle in the panel and led (keyboard backlight) functions as well as some keyboard shortcuts specific to the keyboard layout.

now, i've spilled coffee on the keyboard, used keyd to fix that and address the small pageup/down buttons i keep smashing with my sausage fingers, but one annoyance remained...using my keyboard/laptop at night alot i needed the keys backlit. the functions work, albiet a little funny using fn + f4 to toggle through, but they would always remain lit.

thats not the annoying part, thats fine while im using it. however, if i left the laptop screen open and just let the computer suspend (@15min in settings) the keypad would remain lit, even in the suspend state. now if i closed the lid, autoshutoff works. if i had it plugged in, i could care less, but in battery mode...thats wasteful.

i googled solutions, couldn't find much other than some others with suspend problems, very little reports of system76 laptop users with the same model. i went down the rabbit hole and out the other side and found the post and github of Alex Mohr and <https://github.com/alexmohr/keyboard-backlight>

now i thought i would need to fork the repo and edit their code because the program was originally for a thinkpad (i think). so i reached out to the developer and there was a way to get it working for my laptop!

the program is in the AUR with a funny name, but i downloaded it...
```shell
paru -S tp-kb-backlight-git
```

once installed you can access the program with:
```shell
 sudo keyboard_backlight -h    
```

through the help, although not super obvious, you can actually set the command for the backlight service with the -b option.

with the help of the dev i found the command at:
```shell
cd /sys/class/leds
ls
```

the file shows up as
```shell
system76_acpi::kbd_backlight
```

and the path can be passed with the command:
```shell
keyboard_backlight -b /sys/class/leds/system76_acpi::kbd_backlight/brightness
```

the brightness is key...couldn't get it to work otherwise. now i also set "-t" to 15 and the command works.

now in the readme, alex describes being able to use the service. i was unable to get this functioning right away, so with their help, dug into the settings a bit:
```shell
sudo nano /etc/systemd/system/keyboard_backlight.service
```

now in that file, i needed to make the same adjustments adding the -b and string to the file so it reads like the below:
```shell
[Unit]
Description=Controls the backlight of the keyboard

[Service]
Type=forking
# Options can be passed via command line
ExecStart=/usr/bin/keyboard_backlight -t 10 -b /sys/class/leds/system76_acpi::kbd_backlight/brightness

[Install]
WantedBy=default.target
```

i adjusted the time down to 10 sec for the autooff and saved the file.

with a quick:
```shell
sudo systemctl daemon-reload 
sudo systemctl enable --now keyboard_backlight
sudo systemctl start keyboard_backlight
```

now i tested it all with a reboot. i think its obvious that you have to have the backlights actually on to check operation, but its working well on stops and restarts if i'm suspending, sleeping, whatever. 
for shits and giggles i'm gunna leave the laptop unplugged and lid open overnight and see how it changes the battery use, will report back on that. for now, it fixes that pesky workaround and reminds me how my old macbook pro worked. simple fix, thanks Alex!

## Update: Battery usage
happy to report overnight battery usage with lid open was at 60% down from 100. it was at 20% down from 80% before the backlight autoshutoff. still gunna just close the lid, but much better, and i like the effect.