+++
title = "debian - openRC - update"
date = "2026-04-01"

[taxonomies]
tags = ["openRC", "debian"]
+++

# debian - openRC - update 
 ## 21:40

well, playing around with debian and openrc installs lately, i thought i would see if i could install it on an old resi blackbox video focused NUC. this one i was able to get install working, but had video issues (some strange HDMI jack problem, would literally pulse hdmi viedo output to the the screen) so i decided to try a setting up ssh and then going the headless route. this gave me the opportunity to try installing and configuring openRC over ssh.

following the Cybersecguru article in my last post, i installed just the basic debianOS using the debian testing iso and selected only the ssh server option. i was hopeful this would work, knowing that ssh on deb is openssh from openbsd an should not be systemd dependent. it flawlessly configured to openRC before so i figured why not. 

reboot to prompt check ip address and i again rebooted, this time as a headless and went to access the box from ssh on my laptop.

the rest of the installation went smooth. box came back during each reboot and after the full surgery i had a debian testing install of openRC in a headless. 

the next thing i wanted to try out was setting up tigervncserver on the box. after a few attempts, i decided to go with xfce as a proof of concept and using x11 with vnc viewers has been way less buggy than a wayland install. 

```shell
sudo apt install xfce4 xfce4-goodies 
sudo apt install tigervnc-standalone-server tigervnc-xorg-extension
```

this got xfce to install (though i still couldn't see it), so what was the next thing? going through forums and posts to figure out how to configure and start the xfce box over ssh and then use an ssh tunnel to bring it up in my remmina app.

there were a couple things that needed configured:
/etc/init.d/tigervnc <- needed created to start vnc on boot
vncserver -fp /home/joe/.local/fonts <- help quell a font error
vncserver -localhost -geometry 1920x1080 -depth 24 <- desired command

create the init.d config file tigervnc @ /etc/init.d/tigervnc:
```shell
#!/bin/sh -e
### BEGIN INIT INFO
# Provides:          vncserver
# Required-Start:    networking
# Default-Start:     3 4 5
# Default-Stop:      0 6
### END INIT INFO

PATH="$PATH:/usr/X11R6/bin/"

# The Username:Group that will run VNC
export USER="<username>"
#${RUNAS}

# The display that VNC will use
DISPLAY="1"

# Color depth (between 8 and 32)
DEPTH="24"

# The Desktop geometry to use.
#GEOMETRY="<WIDTH>x<HEIGHT>"
#GEOMETRY="800x600"
#GEOMETRY="1024x768"
#GEOMETRY="1280x1024"
GEOMETRY="1920x1080"

# The name that the VNC Desktop will have.
NAME="resin"

OPTIONS="-name ${NAME} -depth ${DEPTH} -geometry ${GEOMETRY} :${DISPLAY}"

. /lib/lsb/init-functions

case "$1" in
start)
log_action_begin_msg "Starting vncserver for user '${USER}' on   localhost:${DISPLAY}"
su ${USER} -c "/usr/bin/vncserver ${OPTIONS}"
;;

stop)
log_action_begin_msg "Stoping vncserver for user '${USER}' on localhost:${DISPLAY}"
su ${USER} -c "/usr/bin/vncserver -kill :${DISPLAY}"
;;

restart)
$0 stop
$0 start
;;
esac

exit 0
```

change `<username>` to reflect the user with sudo, and change any other details you want to match with your command line:
```shell
sudo chmod +x /etc/init.d/tigervnc
sudo rc-update add tigervnc default
```

now before you run the command you want to initiate the vnc server using the command line:
```shell
vncserver -localhost -geometry 1920x1080 -depth 24
```

this allows you to setup the password you need to access via the vncviewer, creates the config file, and starts the server. the "-localhost" part of the command sets up tigenvnc so that when it initiates the xstartserver it will serve that connection allowing you to establish the ssh connection with localhost:590X in your viewer as the ssh tunnel is running underneath everything

after the above config and password is set, you can then use:
```shell
sudo rc-service tigervnc start 
```

the remaining configuration is done @ your vnc viewer and install/setup variations are platform dependent.

for remmina you click the "+" button at the top left and setup the parameters for access using the VNC plugin method.

just enter the servername: localhost:590x <- "x" meaning the :1 :2 or whatever was shown at the end of the string when you executed the vncserver command, and enter the password you configured in the same step.

continue to the ssh tunnel tab and enable that, then I went with custom IP:22 for the tunnel and password for authentication method. the username and password should be the same as the ssh login user and their password you use to ssh into the box (avoid using root user)!

and that was it! a shiny new xfce box to play around with, all done over ssh. now i plan on setting up tailscale in this again and putting it in a different location and IP, and changing the above in remmina to point to the tailscale address and seeing if that works remotely!