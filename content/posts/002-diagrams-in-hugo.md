+++ 
draft = false
date = 2019-01-27T19:30:13+01:00
title = "Drawing diagrams for use with Hugo - what software to choose?"
description = "I explore what diagram drawing software to use with Hugo."
slug = "diagrams-in-hugo" 
tags = ["hugo","drawing","diagrams"]
categories = ["blogging", "hugo"]
externalLink = ""
+++

Now that I have a blog going the first thing I am pretty certain that I'll need is some tool or software to draw diagrams with. More often than not, when discussing or writing about software, a diagram just helps with explaining what is going on.

In this post I explore my options and explain how I set up my tool(s) of choice.

## The candidates

I quick round of good ol' googling gave me the following options.

* [Mermaid](https://mermaidjs.github.io/) - `Free`
    * Charts are described in the `md` files and rendered in the browser using JS.
* [draw.io](https://www.draw.io/) - `Free`
    * Draw in the browser and save in git as xml. Export as svg or png.
* [Google Drawings](https://docs.google.com/drawings/) - `Free`
    * Save in Google Drive and publish directly to the web.

Naturally, I also came across a few paid services like [Gliffy](https://www.gliffy.com/) and [Lucidchart](https://www.lucidchart.com/), but didn't end up spending any time digging into what they have to offer the free options don't.

## The two winners

While I really like all three options as tools for drawing, I ended up with Mermaid and Google Drawings just because it presented the easiest workflows. Updating a chart in draw.io seemed like a hassle:

1. Open draw.io and locate xml file from git repo
2. Do changes
3. Save xml in git
4. Export to png
5. Republish site
6. Commit all the things

In Google Drawing it would just be to change the drawing and it would automatically be visible on the page. With Mermaid, it is just a matter of changing the content, regenerating the site and committing all the things.

### My Mermaid setup

First, I stole this [mermaid shortcode template](https://github.com/matcornic/hugo-theme-learn/blob/master/layouts/shortcodes/mermaid.html) from [Mathieu Cornic](https://github.com/matcornic) and added it to my Hugo setup.

Then, I needed to add the Mermaid js file. I wanted to have it delivered by a CDN (since I am hosting on github.io), so I just included the following in the footer of my Hugo layout (had to copy the footer from my chosen theme and add it in my `layouts` folder). I got the script tags from the [Mermaid docs](https://mermaidjs.github.io/usage.html) and use the [HasShortcode](https://gohugo.io/templates/shortcode-templates/#checking-for-existence) condition to check if the script is actually needed, i.e. shortcode is in use, before including it.

```html
{{ if ( .HasShortcode "mermaid" ) }}
  <script src="https://unpkg.com/mermaid@8.0.0/dist/mermaid.min.js"></script>
  <script>mermaid.initialize({startOnLoad:true});</script>
{{ end }}
```

Adding charts is then as easy as.

```md
    {{</*mermaid*/>}}
    graph LR;
        A --- B
        B-->C[fa:fa-ban forbidden]
        B-->D(fa:fa-spinner);
    {{</*/mermaid*/>}}
```

Which produces

{{<mermaid>}}
graph LR;
A --- B
B-->C[fa:fa-ban forbidden]
B-->D(fa:fa-spinner);
{{</mermaid>}}

### Google Drawings example

Publising from Google Drawings is as simple as creating the drawing, hitting `File` -> `Publish to the Web` and then including it using normal markdown like:

```markdown
![Sample image from Google Drawing](https://docs.google.com/drawings/d/e/2PACX-1vTK9BdG4Wkck3XhUiJHWHbItePD8GXjbJIkXfnkm4q167QpYTdBJ8foottxPX45j4kh5Q5cWXTA9mJR/pub?w=480&h=270)
```

![Sample image from Google Drawing](https://docs.google.com/drawings/d/e/2PACX-1vTK9BdG4Wkck3XhUiJHWHbItePD8GXjbJIkXfnkm4q167QpYTdBJ8foottxPX45j4kh5Q5cWXTA9mJR/pub?w=480&h=270)

## Summary

Serving diagrams using Hugo on GitHub pages turned out to be pretty easy. I hope that the combination of Mermaid and Google Drawings will serve me well going forward!