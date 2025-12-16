+++
title = "obsidian git repo sync android app"
date = "2025-10-14"
+++

so the basics of this is the app setup essentially has the same access to the git community repository, so the setup between android and linux is pretty much the same, yet different enough that you have to follow the below procedure - this specifically seems to be the way that you have to do it imho

1) if you have another installation of obsidian (like on your laptop/desktop): you must delete the .obsidian files in this folder to get this to play nicely when setting up the repo on android. the easiest way for me to do this was to delete the .obsidian folder using rm -r .obsidian/ on my local gitea folder, then git push to sync with gitea.
2) now setup whatever folder you want to access within android through the files app - i have two folders, so i went to my directory root and added notes and blog folders
3) on a fresh install use the add existing folder and select the folder you want to use for obsidian.  in my case notes
4) now obsidian should take you into the note screen with a fresh directory, no other files present
5) go to the menu and settings cog and add communtity repos then search add and install git
6) enable git plugin and add as many details as you can.  i like to tweak some settings and add my repo username and gitera access token.
7) you will now need to swipe down and enter the same menu you would access with ctrl + p in the desktop version.
8) you are now going to type git clone and select what comes up first
9) add your gitea url to the repo
10) select vault root
11) next question will ask you if you have .obsidian files in the remote dir.  before you select yes...access the files app in android, goto the folder you setup (in my case notes and delete the new .obsidian folder thats in the android folder!!!
12) now back in the app click NO to the prompt about the .obsidian folders.  
13) it will now pull the repo and ask you to restart the app.  do that and verify the folders/files have shown up
