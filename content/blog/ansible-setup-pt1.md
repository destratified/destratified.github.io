+++
title = "this ones a doozy - gitea sync ansible update playbooks for proxmox host - pt.1"
date = "2025-10-31"
+++



goals:
1. setup ansible in lxc on proxmox
2. figure out how the fuck to use it!
3. work backwards and forwards and back again on setup of folder heirarchy
4. make folders into a git repo (gitea for me)
5. sync everything
6. nano hosts file, ansible.cfg
7. build out playbooks for each system [arch], [ubuntu] and [PVE,PVB], figure out ssh, and ssh-copy-id, sshd_config and what the fuck works for passwords and not - still stuck with a password in the ansible.cfg
8. make a comprehensive update-all.yml playbook for a cron job
9. do a buncha bit work putting all the sshd_configs back to #PermitRootLogin password_disabled - maybe I can write a playbook for that!

# so here it goes:
the ansible lxc creation was probably the easiest of all these things to do.  i setup a new lxc container with the usual specs:

Deb13, 2gb RAM, 8GB disk, network etc...this can be really much smaller than i went with

boot into LXC login with root and:
```
apt update && apt upgrade && apt install curl wget ansible sudo 
```

most of these will already be installed, but its good to check, with ansible installed, now i had to figure out how to use it.
ansible will run from any folder, so its up to you how to configure your folder structure and where.  i went with a new ansible folder off of root:
```
mkdir -p /ansible /ansible/playbooks /ansible/inventory /ansible/roles
chmod 775 -R /ansible
touch /ansible/inventory/hosts
touch /ansible/ansible.cfg
touch /roles/roles.txt
touch /ansible/playbooks/playbook.yml
```
this was a basic folder structure i found on the internet, and is probably fine for most applications, some others have set this up for projects using /ansible/<project_name>/playbooks etc, i just needed it for writing playbooks for my proxmox host, so a single dir is fine
with the basic folder structure in place, i wanted to get that structure and files all synced with a gitea repo.
for me, i created a new private ansible repo on gitea, with a readme and nothing else, then followed the instructions to add files/folders and sync remotely with a previous gitea token, results will vary, but it looked something like this:
```
git init
git checkout -b main
git add .
git commit -m "first commit"
git remote add origin https://gitea.destratify.pro/joe/test.git
git push -u origin main
```

then you can check gitea or github, and verify that the folder structure is all there.  above we added some files with touch to basically become placeholders as the folders wont import unless the directorys have content of some sort.

so at this point, we have ansible, we have have a directory synced with git (i use a bash script to update all my gitea folders every 8 hrs JIC) but for now we can start building content in those folders.  

# building out ansible folders/files

so the first things to do are to create a hosts file.  we have a placeholder so you can just nano into that from the /ansible folder
nano /ansible/inverntory/hosts
this can follow either ini syntax or yaml...i went with ini, as it was the format i was borrowing from some guide i can't find again.  here's what mine looks like:
```
#hosts file

[ubuntu]
192.168.50.70
192.168.50.75
192.168.50.8
192.168.50.73
192.168.50.205
192.168.50.14
192.168.50.201
192.168.50.20
192.168.50.30
192.168.50.13
192.168.50.206
192.168.50.74
192.168.50.55

[arch]
192.168.50.16
192.168.50.210

[pve]
192.168.50.2

[pvb]
192.168.50.3

```

this is how i decided to break it up...now is it the right way? dunno.  serves my purpose.  the reasoning behind it is this: i need to update my lxc and vm based on their systemOS, and I have ubuntu/deb, arch and my pve (host),and pvb backup that I want to be able to do individually, or as a whole group.
to be able to talk to them we need to setup ssh-copy-id for each IP and set root login.  what a pain in the ass that is, has to be a way to do that with ansible too, but lets not get ahead of ourselves.
from my host gui, enter console the console using root login.  edit the /etc/ssh/sshd_config
find the commented line, #PermitRootLogin and uncomment and change to yes
save and exit and reboot - i had some problems getting it to work sometimes with a systemctl deamon-reload and systemctl restart sshd so it was just easier to reboot as i went.
next setup a key on the ansible machine.  
```
ssh-keygen -t rsa -b 4096
```
be mindful if you are logged in as something else besides root, i found you just need to be consistent as the dir it gets saved in is the home ~ for the logged in user.  and once that key  is saved you can use ssh-copy-id in the following format:

ssh-copy-id root@192.168.50.5 

this will ask to accept fingerprint, say yes. the password (root user pass in my case) is needed for the machine you are ssh-ing into, after that it should so you the key was added and try logging in....now do that like 17 times ;)

after all that you should be able to use the hosts file to talk to all the machines that you wanna update/upgrade. 

next is the ansible.cfg file, here's mine:
```
#ansible config file
[defaults]
inventory = ./inventory

[privilege_escalation]
become = True
become_method = sudo
become_user = root
become_user_file = ./password.txt

```
here's pretty simple....
in ini format again, under defaults, set your inventory directory so ansible knows where to find the hosts file. 
the rest is pretty obvious - become root, using sudo after sshing into the machine.
i store my password in a chmod 600 password.txt file in my ansible folder, could be anywhere, really. and while troubleshooting and setting this up i had my root password in plaintext here, cause who gives a fuck.

# playbooks

now here's the meat and potatoes.  had lotsa ssh issues, troubleshooting was tedious, but after all that i got it sorted and borrowed a playbook from 
https://www.jacob-swanson.com/posts/automating-proxmox-maintenance-with-ansible/
with a simple command to test connections to all lxcx vms etc in the host file.

```
- name: Ping
  hosts: all
  tasks:
    - name: Ping
      ansible.builtin.ping:
```

for me this was saved as ping.yml in the /ansible/playbooks dir
execute it with the following command
```
ansible-playbook playbooks/ping.yaml
```
now why did i break my hosts file up with [ubuntu][arch][pve][pvb]?
so that when i built out the following playbooks i could run individual playbooks for ubuntu arch etc, with different layouts (pacman vs apt) and then have a unified playbook that would call them all: update-all 

heres the playbooks i have: 
update-ubuntu.yml
```
#update ubuntu lxc, vms
---
- hosts: [ubuntu]
  remote_user: root
  name: update apt
  #serial: 1

  tasks:
  - name: apt update
    apt:
      update_cache: yes
      force_apt_get: yes
      cache_valid_time: 3600

  - name: apt dist-upgrade
    apt:
      upgrade: dist

  - name: remove old packages and clean cache
    apt:
      autoremove: yes
      autoclean: yes
      clean: yes

  - name: Check if reboot is needed
    stat:
      path: /var/run/reboot-required
    register: reboot_required
  - name: Check if reboot is needed
    stat:
      path: /var/run/reboot-required
    register: reboot_required

  - name: End playbook if no reboot is required
    meta: end_host
    when: not reboot_required.stat.exists

  - name: Reboot the system
    reboot:
      msg: "Reboot initiated by Ansible due to system updates"
      pre_reboot_delay: 3600
      post_reboot_delay: 300
      reboot_timeout: 5400
    when: reboot_required.stat.exists   
```
nothing too crazy here.  i have serial: 1 commented out, if you uncomment that, it will step through each IP one-at-a-time.  you should be using dist-upgrade for these, and especially the host and pvb.

update-arch.yml
```
#update arch
---
- hosts: [arch]
  remote_user: root
  tasks:
    - name: Full system upgrade
      community.general.pacman:
        update_cache: true
        upgrade: true

```
nothing unusual here

update-pvs.yml
```
#update pve,pvb
---
- hosts: [pvb, pve]
  remote_user: root
  name: update apt
  serial: 1

  tasks:
  - name: apt update
    apt:
      update_cache: yes
      force_apt_get: yes
      cache_valid_time: 3600

  - name: apt dist-upgrade
    apt:
      upgrade: dist

  - name: remove old packages and clean cache
    apt:
      autoremove: yes
      autoclean: yes
      clean: yes

  - name: Check if reboot is needed
    stat:
      path: /var/run/reboot-required
    register: reboot_required

  - name: End playbook if no reboot is required
    meta: end_host
    when: not reboot_required.stat.exists
  - name: Reboot the system
    reboot:
      msg: "Reboot initiated by Ansible due to system updates"
      pre_reboot_delay: 3600
      post_reboot_delay: 300
      reboot_timeout: 5400
    when: reboot_required.stat.exists   

```
here serial: 1 is un-commented to step through each proxmox server individually.
since my ansible instance is on proxmox, if its kernal is updated, the host will reboot and kill the ansible program.  this doesnt happen often - last time my uptime was 59 days.  you could just reboot ita nd run it again. however, i placed the pvb first in the hosts: [pvb][pve] so that does the proxmox host last, so everything is completed before a reboot JIC

update-all.yml
```
#update whole shebang
---
- import_playbook: update-ubuntu.yml
- import_playbook: update-arch.yml
- import_playbook: update-pvs.yml

```
here we are using the import function, pulling all other playbooks in, and running in the sequence of line items.

# just for funsies
literally ran across the "just" program googling ansible playbooks.  its a pretty nifty program.  it allows you to command run things in the justfile you create and simplifies the commands from the ansible-playbook ... execution.  
install just
apt update && apt install just
then make a justfile, mine lives in the /ansible directory
nano justfile
here's how mine looks:
```
# Show help
help:
    @just --list --unsorted

# Ping all hosts
ping:
    ansible-playbook playbooks/ping.yml

# Update all 
update-all:
    ansible-playbook playbooks/update-all.yml

# Arch Updates
update-arch:
    ansible-playbook playbooks/update-arch.yml

# Ubuntu Updates
update-ubuntu:
    ansible-playbook playbooks/update-ubuntu.yml

# Server (host) Updates
update-pvs:
    ansible-playbook playbooks/update-pvs.yml
```

you can see here i stuck all of my playbook commands in here, they are all broken up by some command name.  
so the sweet thing is that to execute that playbook, you can type "just" and whatever's designated in that file.
just update-all 
just ping
just update-ubuntu 
etc, etc, etc...
now the really cool thing is that you can use this "just" command runner anywhere.  so if you have git directories you wanna update automagically you can just drop a justfile in there with a git fetch section, git pull, git push etc.  and execute that instead of going through each dir.  the possibilities are endless!

so what's next?  im working on the proxmoxer and API calls for my proxmox host, to use with ansible so i can create vms, or lxcs with ansible commands etc.  to be continued.... - 
