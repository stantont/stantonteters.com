+++
categories = ["Development", "Technology"]
date = "2015-07-05T23:04:42-04:00"
description = ""
tags = ["blogging", "hugo"]
title = "Still Working on Blog Setup"
+++
I'm still working on getting the blog setup before I consider it "released."
I've gotten the build setup on CircleCI after trying Wercker, but I'm planning a separate blog post on that topic.
For now, just know that it's nice to just be able to check in to the local Git repo, push it to Github, and get and automated build and release.

I've also setup the blog so it is served via [CloudFlare](https://www.cloudflare.com/).
I assume they'll eventually turn on HTTP/2.0, so I won't have to worry about that since they supply the SSL/TLS certificate.
Again, I'll get into that in another blog post.

<!--more-->

## Adding a Link Content Type

I'm wanting a link content type in addition to the standard post content type.
I'm thinking of how [Marco](http://marco.org) and [Daring Fireball](http://daringfireball.net/) display posts that are links to other articles with a bit of commentary.

Creating the link type isn't difficult at all in Hugo.
You just create a new content item using the command line command `hugo new link/some-article`.
Just using the word "link" as the first item in the path makes it a content item of "link" type.

The pain in the ass part has been getting Hugo and my theme to show only the post and link types on the home page and not also show the plain pages, such as the [About page](/about/). I'm going to have to repeat the same thing for the RSS feed as well.

The theme I'm using currently is [Hyde-X](https://github.com/zyro/hyde-x).
It had a filter applied to the list of items on the home page so only the "post" types were listed.
If I wanted the "link" type as well, I was going to need to modify the theme's layout, so I had to copy it to my own "layouts" folder, making it take precedence over the theme's home page layout, which is named index.html.
I can't remember the exact issue, but I had some issues with the combination of my layouts and the themes, so I just copied all of the theme's layouts to my layouts directory.

The home page template filter uses the Hugo `Where` template function to limit the home page to just blog posts. It looks like this:

    {{ $paginator := .Paginate (where .Data.Pages "Type" "post") }}

That didn't appear as though it would work for multiple content types, so I eventually wound up looking at the `Where` function in the Hugo [template_funcs.go file](https://github.com/spf13/hugo/blob/v0.14/tpl/template_funcs.go).
I found that the `Where` function can take another argument between the field, `Type` in this case, and the string to match, "post" in the example above.
One of the arguments additional arguments is `"in"`, which can match a value in a slice or array.
I am not that familiar with the Go templates yet, but I thought I'd try just adding a slice directly in the template.
As expected, that did not work.
Then I tried adding a new list in my site config.
Surprisingly, that did not work either.

I played with that for a while, trying to get the `Where` function to use the array/slice, but nothing worked.
Just for the hell of it, I tried using a string with the two words separated by a space.

    {{ $paginator := .Paginate (where .Data.Pages "Type" "in" "post link") }}

That works!
So I changed my config setting using a slice to be a string.

I should investigate the `Where` function some more and open an issue with Hugo if it really is a problem, but I'll get back to that.
I really just want to get the blog in a good state for now.

## Adding a Permalink Icon

Since the main link for a "link" content type will go to the link on the other site, not my link entry, I needed something to point to the entry on my site.
Daring Fireball uses a star, and Marco uses what looks like an infinity symbol.
I went with Marco's style since it looks more like the generic "link" symbol.
I had created a link template, so I added this permalink "icon" to the home page list and the post and link templates.
For the link header I made the text underlined so it looks like a link and added the external site icon next to it.
Just to reduce duplication, I created a permalink partial template to include in the three templates mentioned above.

When I say "icon", I mean the font-type icons in [Font Awesome](http://fortawesome.github.io/Font-Awesome/), which Hyde-X already has included and the [Github Octicons](https://octicons.github.com/), which I added.
Having two icon sets is overkill, but it's something else I'll try to go back and improve.

## Post Summary

The Hyde-X theme home page just has a link to the post without any text before the "Read On" link beneath the title unless you've filled in the "description" front matter for the post.
I really prefer to use the text of the post, whether partial or whole, on the home page.
Hugo builds a summary for each page, but it's plain text without any tags, which looks like a big lump of text.
Reviewing the code for the Page code made it apparent if you include a special HTML comment, <code>&#60;&#33;&#45;&#45;more&#45;&#45;&#62;</code>, it will give you the text  above the comment including the tags.
I think using this special comment is fairly standard in CMS's and static site generators.
So I switched my home page layout to use the Hugo summary instead of the front matter description, but I'll have to always remember to add the comment tag.

That's enough changes for now. I think I'll check in my changes and this post and publish!
