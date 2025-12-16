+++
title = "forgejo migrations"
date = "2025-11-26"
+++

so, just for shits and giggles I switched to FOSS forgejo from gitea. process was basically this:
1) setup new vm or lxc for debian trixie - i just used an lxc template i had and tweaked the settings
2) get lxc updated and dist-upgraded.  i used the forgejo-sqlite: follow online setup of repos for forgejo using debian 12 (also works for trixie)
```
apt update && apt dist-upgrade && apt install forgejo-sqlite && apt install cockpit
```
3) i also installed cockpit just for funsies, lets me know all the webserver stuff is good.
4) go thru the installer by logging into your localhost:3000 
5) i did a pretty vanilla setup just to get things going, it nagged me about an administrator so i did that too
6) now i setup a token in gitea for http access and used it to migtrate all of my repos to the forgejo.  then on my local laptop, i deleted all my gitea folders and cloned the migrated repos from forgejo - yes this required another token.
7) probably the most time consuming thing was doing this manually for 5 repos...i can't  imagine if i had 70 or something.
8) i also deleted and recreated my ansible folders in my ansible lxc with the new forgejo one. this worked exactly as i had hoped. tested with my justfile just to be sure :)
9) so after all this and testing, i went in and adjusted my obsidian installs on my phone to the forgejo repos and also did this on my laptop.
10)  i backup/mirror all these repos to a private github as an additional safety.  this was the same method that i used with gitea.  erase all existing mirrored repos, create new ones (public and private as needed), and added back in the forgejo mirror that goes to public on github where my blog pulls posts from.
11)  everything works and I think forgejo is just as pleasant to use as gitea, even a little snappier than gitea was. the big hurdle i see if for those peeps who have many repos.  perhaps theres an ansible way of doing this.
