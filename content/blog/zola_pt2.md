+++
title = "zola_pt2"
date = "2025-12-17:12:33"
[taxonomies]
tags = ["zola", "webdev"]
+++

# zola_pt2
the next step i was having a hard time finding out how to do, so i went to the creator of the  themes github...

<https://github.com/ebkalderon/terminus/blob/main/config.toml>

there, i took a peak at their config.toml and was able to discern that the  menu needed added to the `extra` section.  this was the code block i needed:
```
[extra]
close_responsive_menu_on_resize = true
copy_button = true
favicon_emoji = "👨‍💻"
fediverse_creator = "@ebkalderon@hachyderm.io"
layout = "center"
show_default_author = false

main_menu = [
    { name = "blog", url = "blog", trailing_slash = true },
    { name = "archive", url = "archive", trailing_slash = true },
    { name = "tags", url = "tags", trailing_slash = true },
    { name = "projects", url = "projects", trailing_slash = true },
    { name = "github", url = "https://github.com/ebkalderon/terminus", new_tab = true },
]
```

i pruned that down, so i could test things one at a time

```
[extra]
# Put all your custom variables here


main_menu = [
    { name = "blog", url = "blog", trailing_slash = true },
    { name = "tags", url = "tags", trailing_slash = true },
    { name = "about", url = "about", trailing_slash = true },


```

so far working great...i have the tags to figure out yet, but the blog and about work fine...

