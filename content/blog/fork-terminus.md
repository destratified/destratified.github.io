+++
title = "fork-terminus"
date = "2026-01-15"

[taxonomies]
tags = ["webdev", "zola", "github"]
+++

# fork-terminus 
 ## 15:52
 trying to fork themes/terminus with an edit in base.html allowing umami to check webpage stats...see how this goes. so not sure if anyone even looks at this. haha...but anyway. 

fork the [terminus](https://github.com/ebkalderon/terminus) github repo with an edit in main branch to themes/terminus/base.html

i added the script from the umami website settings, find that by highlighting and clicking edit for the website.

i had to open up base.html for editing in the github editor and then pasted it into the `<head>` section of the base.html file, clicked commit changes and asked it to save as a fork. this pulled the repo into my github and then i had to delete the old git submodule and add my own.

this was done by:
```
nano .gitmodules
rm .gitmodules #i had no other submoodules so i deleted whole file
nano .git/config #i had no refs to the submodule here so exited
git rm --cached themes/terminus
rm -rf themes/terminus
git commit -m "removed submodule"
git push
git submodule add https://github.com/destratified/terminus.git
cd themes/terminus/templates/base.html #check that the file i updated synced
cd themes/terminus
git pull #updates submodule
cd / #whatever your repo root is
git add .
git commit -m "new submodule"
git push
```
voila!