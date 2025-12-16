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

## downloading and init

first i created a new directory in my laptops ~/forge folder in the way recommended by github
`sudo mkdir -p ~/forge/<user>.github.io`

then you want to download your necessary programs: for me this was git and zola.  some installs will require another git program when you run the following command, but on arch mine just worked, or id done it at some point in the past.

first git init
then run
`gh repo create destratified.github.io --public --source=.
`
this command creates the public version of the repo, syncing it and allowing you to work in your local folders. you might have to download another git prog to run the above command, mine didn't, but i remember trying to do that on my server.  you also will likely need to authorize and sign into your github account.  there are lots of little steps, you can choose https or ssh, i did https and a token but once you have that going it will setup the repo and you can verify with your githib repo list.

next i did a quick `zola init force`  in the root of that directory.  the --force allows it to move past the .git files.

you can do as much syncing as you like throughout this process or wait until the end.  
add a `.gitignore` file with the contents public/ to prevent syncing the public dir to github, it will create its own later.

this pretty much gets ya up and running

## content and index files

here I had some files stored in my ~forge/blog that i wanted to move over to the new dir/contents
I just copied using cp -r from directory to directory
i had been building zola before i decided to go this route, so i had the other `_index.md` file s sorted out. 
my dir structure was contents with and `about.md _index.md`and a /blog folder.  the `_index.md` file has the contents below:

```
+++
title = "home"

[extra]
section_path = "blog/_index.md"  # Where to find your posts.
max_posts = 5  # Show 5 posts and a link to blog section on home page.
+++
```

the +++ enclose the contents of the instructions like a .yaml file and obviously the title instructs the zola build command how to orient things in the index.html file it builds in the /public folder later
all my .md files are in the /blog folder so I also need another files this index file points the homepage to pull info from the blog directory

in the /blog folder i have all the posts i want to publish, each has a front-matter that is similar to below...i use a template for a webpost in obsidian, which i am still using to write posts, and update the git repos locally, syncing to the github repo main branch

```
+++
title = "after proxmox reboot, found a couple issues"
date = 2025-08-11
+++

```

so the next thing that was necessary was to add that index.md file to the blog/ folder so that the zola build command understands how to handle the files in the directory.  for now i settled on some sorting by date and thus needed the front-matter in each .md file to aid in that.  the blog/index.md file looks like this:

```
+++
title = "blog" 
paginate_by = 5 
sort_by = "date" 
+++
```

now that we have essentially got all of the content stuff together, the config.toml file in the root directory needed some tweaks:

```
# The URL the site will be built for
base_url = "https://destratified.github.io"

theme = "terminus"
title = "ink the ghost"

# Whether to automatically compile all Sass files in the sass directory
compile_sass = true

# Whether to build a search index to be used later on by a JavaScript library
build_search_index = true

[markdown]
# Whether to do syntax highlighting
# Theme can be customised by setting the `highlight_theme` variable to a theme supported by Zola
highlight_code = true

[extra]
# Put all your custom variables here

```

here i just added a name for the page, correct the https://<username>.github.io that you intend to serve to and some basic stuff that comes with a generic config

you may notice that terminus is listed as the theme.  you will also want to add that theme as a submodule using the command below.  make sure you are at the root of the directory

`git submodule clone https://github.com/ebkalderon/terminus.git themes/terminus`

now you have pretty much everything you need.  add a final file that helps to skip the default theme that github uses when building and deploying the webpage.

`touch .nojekyll`

# github stuff
