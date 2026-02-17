+++
title = "archive tab for zola"
date = "2026-01-04"

[taxonomies]
tags = ["zola", "webdev"]
+++

# archive tab for zola 
 ## 10:29
 beating my head against the wall for almost 3 hours on this one, so here it goes:

i was attempting to follow the procedure for adding an archive to the tabs on my homepage for grouping by year in decending order.

i was following the example by Peter Babic from his page <https://peterbabic.dev/blog/add-archive-into-zola/> the below code is saved as /templates/archive.html

the code snippet was close:
```shell
{% extends "index.html" %}

{% block content %}
  {% set section = get_section(path="blog/_index.md") %}
  {% for year, posts in section.pages
    | sort(attribute="date")
    | reverse
    | group_by(attribute="year") %}
  <div class="archive">
    <h2>{{ year }}</h2>
    <ul>
      {% for post in posts %}
      <li>
        <time>{{ post.date | date(format="%d-%h") }}</time>
        <a href="{{ post.permalink }}">{{ post.title }}</a>
      </li>
      {% endfor %}
    </ul>
  </div>
  {% endfor %}
{% endblock content %}

```
mine kept bonking out even after id sorted out all the other things i needed....and the page would render if i didn't add the `{% extends "index.html" %}` so i finally realized after some websearching and duck.ai that i needed to find out how the templates were using the code block above.  when i used the complete code, nothing would show besides my main header, tabs and footnotes.

perhaps a quirk of zola and terminus, i found that if i changed Peter's code, the `{% block content %}` was the source of my issue.  looking at the /themes/terminus/index.html code the `block content` i was referring to was called `{% block main %}`.  makes sense as it was doing the right work, but had no place to put the content.  a quick code change allowed it to populate properly.

```shell
{% extends "index.html" %}

{% block main %}
  {% set section = get_section(path="blog/_index.md") %}
  {% for year, posts in section.pages
    | sort(attribute="date")
    | reverse
    | group_by(attribute="year") %}
  <div class="archive">
    <h2>{{ year }}</h2>
    <ul>
      {% for post in posts %}
      <li>
        <time>{{ post.date | date(format="%d-%h") }}</time>
        <a href="{{ post.permalink }}">{{ post.title }}</a>
      </li>
      {% endfor %}
    </ul>
  </div>
  {% endfor %}
{% endblock main %}

```

the other tidbits that were needed to get this working:

i had to adjust my main menu section in my config.toml in /:
```shell
#main menu tabs for pages blog, all tags listed in front-matter and about
main_menu = [
    { name = "blog", url = "blog", trailing_slash = true },
    { name = "archive", url = "archive", trailing_slash = true },
    { name = "tags", url = "tags", trailing_slash = true },
    { name = "about", url = "about", trailing_slash = true },
]
```

and similar to my tag-listing.md, i had to create a /blog/archive.md file with the following code:
```shell
+++
title = "Archive"
template = "archive.html"
+++

```

this finally allowed my page to render with 2025 results, as of this post, 2026 should now be visible as well :)