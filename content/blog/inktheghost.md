+++
title = "inktheghost"
date = "2025-12-19"

[taxonomies]
tags = ["zola", "webdev"]
+++

# inktheghost.com
 ## 17:53

wanting to move my blog to a domain name i have setup instead of the name github pages give me i did a little searching.  borrowing from the excerpt from <https://thedataquarry.com/blog/static-site-zola/> from which i've pulled some random zola information before...i setup a name cheap domain to match my webpage - holiday discounts out rn! i found inktheghost.com was not taken. yay. and proceeded to pay and setup as shown by <thedataquarry.com>

following his namecheap directions...i already have a namecheap account so that part was easy. i did the below:

"I created the list of A records to point my apex domain to the IP addresses for the GitHub Page, as per the docs. Note the `CNAME Record` on the last line (this is important). On the Zola side, I simply create a file called `CNAME` in the `static` folder, and add the following line to it:"

```
inktheghost.com
```

too easy it seems. well of course that was the case.  i push the contents, waiting for the rebuild and blammo, fucking page was rendered all wrong.  quick panic, delete files and gitpush again while i sort it all out.  maybe this will help you too!  
# DONT FORGET TO CHANGE THE URL REFERENCE IN CONFIG.TOML

yep that was it. add CNAME file to static/ again, change config.toml file in / to the proper URL


```
# The URL the site will be built for
base_url = "https://inktheghost.com"

theme = "terminus"
title = "ink the ghost"

```

re-push and page looks correct, whew.  

now i got a static generated, zola-based, custom domain-named webpage hosted on github pages, using a repo that i can edit on my laptop in obsidian notes synced with git and can easily push updates :) fuck ya.