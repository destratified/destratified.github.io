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
- 
- 
