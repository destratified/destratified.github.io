+++
title = "zola pt1"
date = "2025-12-16"
+++



# zola

i wanted to simplify my website currently at blog.destratify.pro to get away from all the bullshit that comes along with wordpress.  also i was already using a github repo to do the site serving so i started poking aroung and found zola. first i tried using zola with a forgejo repo and then mirroring to codeberg...codeberg pages was a bust, buggy, kept fucking up the formatting.  i then tried multiple ways of getting github to work and finally found a way to make it all happen. this enabled me to use a local repo and then "serve" it to github.  github actions would always rebuild the static site on boot.  now that i have it working i'm going to play around with formatting, adding tabs, pages, getting the about page to show up and cross-referencing with tags.  one of the nice features of zola is that you can always test before you push to github with the local zola serve feature.  so here is what this looks like:
- setup a directory to work in
- download zola and git programs
- init the repo
- build your content into the zola framework
- add your `_index.md` files 
- do your whole github action stuff
- change the source for the deployment to gh-pages
- add a new file and watch it go!

## init

first i created a new directory in my laptops ~/forge folder in the way recommended by github
`sudo mkdir -p ~/forge/<user>.github.io`

then you want to download your necessary programs: for me this was git and zola.  some installs will require another git program when you run the following command, but on arch mine just worked, or id done it at some point in the past.

first git init
then run
`gh repo create destratified.github.io --public --source=.
`
this command creates the public version of the repo, syncing it and allowing you to work in your local folders. you might have to download another git prog to run the above command, mine didn't, but i remember trying to do that on my server.  you also will likely need to authorize and sign into your github account.  there are lots of little steps, you can choose https or ssh, i did https and a token but once you have that going it will setup the repo and you can verify with your githib repo list.

next i did a quick `zola init force`  in the root of that directory.  the --force allows it to move past the .git files in the dir and create the dir structure needed.

you can do as much syncing as you like throughout this process or wait until the end.  
add a `.gitignore` file with the contents public/ to prevent syncing the public dir to github, it will create its own later.

this pretty much gets ya up and running

## content

here I had some files stored in my ~forge/blog that i wanted to move over to the new dir/contents
I just copied using cp -r from directory to directory
i had been building zola before i decided to go this route, so i had the other `_index.md` file s sorted out. 
my dir structure was contents with and about.md _index.md_