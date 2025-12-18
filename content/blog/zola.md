+++
title = "zola"
date = "2025-12-17 : 09:33"
[taxonomies]
tags = ["zola", "webdev"]
+++
# zola

i wanted to simplify my website currently at blog.destratify.pro to get away from all the bullshit that comes along with wordpress.  also i was already using a github repo to do the site serving so i started poking aroung and found zola. first i tried using zola with a forgejo repo and then mirroring to codeberg...codeberg pages was a bust, buggy, kept fucking up the formatting.  i then tried multiple ways of getting github to work and finally found a way to make it all happen. this enabled me to use a local repo and then "serve" it to github.  github actions would always rebuild the static site on update.  now that i have it working i'm going to play around with formatting, adding tags, a menu and getting the about page to show up.  one of the nice features of zola is that you can always test before you push to github with the local zola serve feature.  so here is what this looks like:

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
i had been building zola before i decided to go this route, so i had all the other `_index.md` files sorted out. 
my dir structure was contents/ with an `about.md _index.md`and a /blog folder.  the `_index.md` file has the contents below:

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

if you wanna check and make sure it all looks good...you can also do a `zola serve` and follow the link to the local webpage before you upload everything to git hub.  if you like how it all looks

```
git add . 
git commit -m "update"
git push
```
# github stuff

now with all that done and everything pushed to the repo, lets do another few things to get this going...now with that said I had this fuckered up for a while before i realized my mistake, so hopefully this will help you also.

right now you should just have a "main" branch.  if you have more than that, delete the second branch...this is also how you can start over if something goes wrong.  you will need two things done.

- create a token (classic) with repo access
- create a github action

first create the token by going to user > settings > developer settings > personal access tokens > tokens classic and create a token i used the name webpage, it doesnt really matter, just copy it before you navigate away from the page.

then goto your repo you setup > settings > Secrets and variables > actions > new repo secret
set the name to TOKEN and add the token into the field below and save

next you will setup an action.  go back to the repo main page > actions

here lots of options, but i just pick the simple basic and then delete everything in it and copy the code in below:

```
# This is a basic workflow to help you get started with Actions

name: CI

# Controls when the workflow will run
on:
  # Triggers the workflow on push or pull request events but only for the "main" branch
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      - name: Checkout main
        uses: actions/checkout@v4
      - name: Build and deploy
        uses: shalzz/zola-deploy-action@v0.21.0
        env:
          GITHUB_TOKEN: ${{ secrets.TOKEN }}
```
click commit and the repo will start the commit, update and build the webpage

the last thing you need to do is goto settings > pages
there you will need to edit the the branch from main to gh-pages and click save.
from this point on, every update to main should trigger the action to rebuild the static site from scratch, by posting this its confirmation of it working!

next up zola pt2...adding tags, section pages and an about, blog, and tags menu on the homepage.  also i want my favicon in the webtab, so i'll have to figure that out too.