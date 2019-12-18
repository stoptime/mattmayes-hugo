---
title: "Hugo and Wrangling Hyde-Hyde"
date: 2019-12-15T19:33:33-06:00
tags: ["hugo", "dev"]
---

So I'm just getting started with Hugo, and decided to go with, and tweak, a classic theme: [hyde-hyde](https://themes.gohugo.io/hyde-hyde/). Off the bat, while the hugo docs are good - here's a few things that helped things _click_ for me.

1. When you just run `hugo` - it creates a public directory, and this is where your files are served from (and what you would upload to a host). If things seem off or just weird, look for this directory and if exists, delete the whole thing - it's easy to recreate by just running `hugo` again.

2. If you run `hugo server` it does NOT create that directory.

3. Of course you'll probably want to add your css, [this is how you can do it](/posts/2019/hugo-add-custom-css-properly/) (if your theme doesn't do it already). That post probably applies to js as well.

4. Your theme probably has an `exampleSite` folder in it - check out the `config.toml` file in there for what your theme can do. You may just want to copy this into your `config.toml` file and edit it.

5. Use git. Whenever your site is in a a good place while you're figuring things out, make a commit you can revert back to before trying new stuff. 

6. While it's not hard to override theme files (which the link in point 3 goes into), it prevents you from easily switching themes. If you're tinkering with themes, consider just editing the theme itself until you've settled on one. Then git revert/stash your changes (assuming you installed your theme as a submodule) or re-download it. Then turn those changes into theme overrides (assuming you want to get theme updates).

7. Check out a theme's `your-site/themes/hyde-hyde/archetypes/default.md` for what your can setup in your own `your-site/archetypes/default.md` file, for what front matter gets setup when you run `hugo new page.md`

8. Aaaaand...

## Treat Your Homepage as Both a Page and a Post Listing

Hyde-hyde's home page is a post listing by default - but I wanted to also have a message before the listing. Here's how to do it.

1. Under your `content` directory, create an `_index.md` entry and put what you want in there.

2. Copy hyde-hyde's `your-site/themes/hyde-hyde/layouts/index.html` file into: `your-site/layouts/index.html`

3. Then edit your new file, before the `post-list` - enter this:

```html
  <main aria-role="main">
    <header class="homepage-header">
      <h1>{{.Title}}</h1>
      {{ with .Params.subtitle }}
      <span class="subtitle">{{.}}</span>
      {{ end }}
    </header>
    <div class="homepage-content">
      <!-- Note that the content for index.html, as a sort of list page, will pull from content/_index.md -->
      {{.Content}}
    </div>
  </main>
```

And now you have your new home page with a custom message as well as a post listing.