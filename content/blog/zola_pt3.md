+++
title = "zola_pt3"
date = "2025-12-17:14:01"
[taxonomies]
tags = ["zola", "webdev"]
+++

# tags in zola website

the next thing i wanted to do was two-fold...
- insert tags into front-matter in notes
- build a template in obsidian for duplication

so first things first...
tags get added by the creation of :
- tags.html
- tags-listing.md
- modification of the config.toml @ root
- adding tags to your front-matter in your `.md` files

the tags.html file is located in the templates folder.  i used nano and added the following code:

```
{% extends "base.html" %}
{% block content %}
<header>
    <h2>Tags</h2>
</header>
{% set blogtags = get_taxonomy(kind="tags") %}
{% for tag in blogtags.items %}
    <h3 id="{{ tag.name | slugify }}">{{ tag.name }}</h3>
    <ul>
        {% for post in tag.pages %}
            <li>
                <time datetime="{{ post.date }}">{{ post.date }}</time> –
                <a href="{{ post.permalink | safe }}">{{ post.title }}</a>
            </li>
        {% endfor %}
    </ul>
{% endfor %}
{% endblock content %}

```

this allows the tags page to get build using the slugs that you will add in your front matter.  next create a file called `tag-listing.md` in the root of your posts section, mine is /blog.  it should be pretty simple

```
+++
title = "Tags"
template = "tags.html"
+++   

```

this is only a file containing front-matter that the tags tab in the menus created in the config.toml file we did in pt2.

we also want to modify the config.toml file at root to enable the taxonomies we want to use in the front-matter notes of the posts

this is under the initial part of the file, not under a section as show below:

```
theme = "terminus"
title = "ink the ghost"

# Whether to automatically compile all Sass files in the sass directory
compile_sass = true

# Whether to build a search index to be used later on by a JavaScript library
build_search_index = true

taxonomies = [
    { name = "tags", feed = true },
    { name = "categories", feed = true, paginate_by = 5 },
]

```

since i don't know what i'm really doing i used only the tags and categories from the example i'm following in zola_pt2 seemed to be all i needed.  

now the last bit is adding the front-matter to your blog posts.  for each post you would like to categorize, you will want to use a tag...for this page for example I used:

```
+++
title = "zola_pt3"
date = "2025-12-17"
[taxonomies]
tags = ["zola", "webdev"]
+++
```

after uploading/rebuilding my site, the tags tab in the menu now shows content, and each tag that i've used has a counter and link to a page that will let me see which posts have those tags and i can look at the posts from the links.

now, to make this a little better...

since i am blogging from my laptop usually (in obsidian notes), I have the ability to use templates to embed this front-matter in a template and when i create a new `post.md` file in my blogs/ folder i can insert that template and the title, date most of the front matter data is populated.  with a couple quick edits the file is ready to start filling with content.  here's my template:

```
+++
title = "{{title}}"
date = "{{date}}"
[taxonomies]
tags = ["tag1", "tag2"]
+++

# {{title}}

```

i just edit the tags and away i go.  i followed the link below to figure out how to setup the templates folder in my vault, remember keep it simple!

<https://obsidian.rocks/getting-started-with-templates-in-obsidian/>