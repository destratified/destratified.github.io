+++
title = "codeberg pt1"
date = "2025-12-01"
+++


# eliminating github
so i moved all my githhub repos (which were really backups of my forgejo repos - in case the SHTF) to codeberg and setup the mirrors from forgejo like i had with github, just to the codeberg repos instead.  with that done  I only have 2 things happening on Github rn:
1) adding and pushing .md files to blog repo updates my blog on wordpress - looking to move that to codeberg pages or something else.
2) logging into tailscale using my same github account - i dont have a great workaround for this, but its just for  authenticating.
so with all repos moved to codeberg including a new mirror, im exploring now how to use the repos directly to create/host my webpage and thus get rid of one of my containerized instances (wordpress) and getting it off my server.
since i'm already using forgejo, this seems like it should be logically easy.  lets see.
current flow should be
- create md file on laptop in obsidian that updates and pushed file to forgejo instance
- forgejo repo is private and has a mirror setup to update on commits to codeberg repo
- codeberg blog repo updates and webpage updates showing new md file as new blog post
simple right? well, upon inspection it looks like i need woodpecker or zola, maybe both and the codeberg pages instance (perhaps) to get this all done, maybe there's another option im missing?
i feel like the wordpress install was messy and is harder to maintain AND update, so simplification would be great there.  anyways, maybe we will call this part 1 and do some research...rn this is to check if all my mirror syncs are working and updating the existing website while i figure out how to do all of this another way! lets see...still having issues with timeouts, ill have to get into debugging, but it looks like there's an issue with webpage serving.  i might try a fail2ban as well
looking at how to set that up exactly, but after an update, i've had 12 hrs no downtime so so far so good.  seems like connection issues sending updates on commits to mirrors.  maybe an action would be a preferred method for instant updating?

