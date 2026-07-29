+++
title = "linux-surface fedora"
date = "2026-07-29"

[taxonomies]
tags = ["surface pro9", "fedora", "rawhide", "linux-7.2-rc5"]
+++

# linux-surface fedora 
 ## 09:13

my initial goal was to get the fedora implementation of linux-surface kernel working and get the surface pro9 i'd bought plenty usable. but of course then i wanted more. the fedora instructions for the linux-surface kernel mods do work, relatively out of the box, and get you up to kernel 6.19 currently {{date}}, but i want to run a more bleeding edge version - which is still relatively stable on fedora rawhide and with a repo update to rawhide, i broke all the goodies that went with the fedora44 install.

i used a mixture of solutions here and with some internet advise, searching reddit, and other nearly similar work arounds, decided on the following:

- install latest kernel
- patch using linux-surface repos
- build iptsd from source
- build a repeatable script that would let me do easier next time

### installing latest kernel

the easiest instructions wound up in the github documentation for the Linus Torvalds github main kernel repo (am i saying that right?) and linked to here [https://github.com/torvalds/linux/blob/master/Documentation/admin-guide/quickly-build-trimmed-linux.rst#id17](kernel)

this is a handy kernel build sheet for a one-time kernel install, of course there's options here, but the tl;dr is there and copy/paste will get you the latest kernel (7.2-rc5). the instructions worked part way for me.

