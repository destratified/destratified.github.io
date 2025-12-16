+++
title="getting backup to work on desktop via PVE"
date = "2025-09-11"
+++



# Update TL;DR
so i ended up having some issues getting the proxmox-backup-client command to run, using sudo and after getting into the forums realized that i could su to root, move the file there, and append changes, chmod to execute and add the cronjob under the root user.  this eliminates the need all together for the sudo command!

heres the updated file i saved in the /root/ directory:
```
#!/bin/bash

##Setting environmental variables
export PBS_USER=archy
export PBS_PASSWORD=<urpasswd>
export PBS_REPOSITORY=archy@pbs@192.168.50.3:8007:not-server2

##Checking network connectivity
ping -c 1 192.168.50.3 | grep "bytes from" > /dev/null
if [ $? -eq 0 ]; then
    echo "$(date '+%Y-%m-%d %H:%M:%S') - Network is connected" >> /home/joe/backup.log
else
    echo "$(date '+%Y-%m-%d %H:%M:%S') - Network is not connected" >> /home/joe/backup.log
    echo "$(date '+%Y-%m-%d %H:%M:%S') - Backup failed" >> /home/joe/backup.log
    exit 1
fi

##Sudo command with -E to preserve the env variables
proxmox-backup-client backup root.pxar:/ --include-dev /home

#testing backup error loop
if [ $? -eq 0 ]; then
    echo "$(date '+%Y-%m-%d %H:%M:%S') - Backup completed successfully" >> /home/joe/backup.log
else
    echo "$(date '+%Y-%m-%d %H:%M:%S') - Backup failed" >> /home/joe/backup.log
fi  
```

you can test by running the script backup.sh to make sure it works without erroring out with the above changes.

# OG text

so i created a bash script and was having it err anytime i wasnt actually logged into a terminal
after some poking around it hadn't executed as sudo for the bash script in a while and realized that there was an issue passing sudo password to the backup command
i will preface this with i am still not sure if this is working, but i will know by 4pm today.  here's my script:
```
#!/bin/bash

##Setting environmental variables
export PBS_USER=archy
export PBS_PASSWORD=@!Moksha00
#export SUDO_PASSWORD=$(cat /etc/backup_password)
export PBS_REPOSITORY=archy@pbs@192.168.50.3:8007:not-server2

##Checking network connectivity
ping -c 1 192.168.50.3 | grep "bytes from" > /dev/null
if [ $? -eq 0 ]; then
    echo "$(date '+%Y-%m-%d %H:%M:%S') - Network is connected" >> /home/joe/backup.log
else
    echo "$(date '+%Y-%m-%d %H:%M:%S') - Network is not connected" >> /home/joe/backup.log
    echo "$(date '+%Y-%m-%d %H:%M:%S') - Backup failed" >> /home/joe/backup.log
    exit 1
fi

##Sudo command with -E to preserve the env variables
sudo -E proxmox-backup-client backup root.pxar:/ --include-dev /home

#testing backup error loop
if [ $? -eq 0 ]; then
    echo "$(date '+%Y-%m-%d %H:%M:%S') - Backup completed successfully" >> /home/joe/backup.log
else
    echo "$(date '+%Y-%m-%d %H:%M:%S') - Backup failed" >> /home/joe/backup.log
fi   
```

and the problem seemed to be in this line of code:
```
sudo -E proxmox-backup-client backup root.pxar:/ --include-dev /home
```
some digging on the webs (i dont use google, cause you know privacy, data harvesting blah blah blah) recommended using a password setting variable and also adding my user and allowing passwordless execution for the bash scripts.  now this seems to include the use of sudo from within the scripts as well, but we shall see...at 4pm tbd

so for the sudo command thingy, you can enter the details to fit the command name and user, but it follows the format below:
```
echo "username ALL=(ALL) NOPASSWORD: /path/to/script.sh" | sudo tee /etc/sudoers.d/script-no-password
```

mine is:
```
echo "joe ALL=(ALL) NOPASSWD: /home/joe/backup.sh" | sudo tee /etc/sudoers.d/script-no-password
```

now i use arch, so i chose to install cronie as i was struggling with the above script (realizing now that the sudo fix should allow me to use systemctl services to do the same as cronie) but i have a little more familiarity with cron, so i wanted to use it for debugging a bit.  here's how i got that going on arch:
```
sudo pacman -S cronie
export EDITOR=/usr/bin/nano && export VISUAL=$EDITOR
```

now you can setup your cron to run, im using some debug stuff and wanting to run daily so here's how that looks:
```
0 16 * * * /home/joe/backup.sh 2> debug.log 2>&1
```

the export variables tell the system to use nano instead of vi, i don't use vi, so this got it going in my favorite editor, and ctrl+x and its saved

this is a good time to make sure you  had the script made executable as we are testing with the following command, so if you haven't already:
```
sudo chmod +x /path/to/script.s
crontab -l | grep -v '^#' | cut -f 6- -d ' ' | while read CMD; do eval $CMD; done
```

the crontab command above will cycle through and test execution of all of the commands in crontab sequentially, so just keep that in mind.  I only have this one and a gitea script for updating my repo, so nbd to have it execute them all.

ill report back later if this is the fix, but with some network debugging and script debugging i should get updated on the script as its processed

