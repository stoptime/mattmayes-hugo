---
title: "Hugo: Add Handy Css Classes to the Body Tag"
date: 2019-12-19T10:08:48-06:00
tags : [ "dev", "hugo" ]
showpagemeta: true
showcomments: true
---

It is so handy to have some css classes on the `<body>` tag. When working in Drupal, I like to at least have the content type as a css class, "article" or "page" or whatever. In Hugo, I also put the content type as a class on the `<body>`, as well as the filename/path, like so:

```go
<body class="{{ .Type }} {{ with .File }}{{ .TranslationBaseName }}{{ end }}">
```

In the hyde-hyde theme this is done in the `baseof.html` file in the `_default` folder, so I just copied that into:
`my-site/layouts/_default/baseof.html` and made the above change. Now if you inspect the source of this page, you'll see:

```html
<body class="posts hugo-add-handy-css-classes-to-body-tag">
```

The `posts` comes from the top-level content folder name (content/**posts**/2019/hugo-add-handy-css-classes-to-body-tag.md), but you can also declare "type" in your front matter if you wanted something different:<br>
`type: "pancake"`

Likewise, if I had a post inside a top-level folder called "music" - then `.Type` would be music, no need to put that in the front matter if you are happy with it (it's certainly the easier, automated way of assigning type). 

I originally sought this out because I wanted to hide the meta front matter on all "pages," but not "posts" - and with this and some css you can now do that very easily. However there's a **better way** in hyde-hyde as well as other themes, just set `showpagemeta` to `false` in your front matter. You can hide Disqus/comments in the same way with `showcomments` - I added these to my `my-site/archetypes/default.md` file, so it now looks like this and these handy variables are ready to assign:

```md
---
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
tags : [ "dev" ]
draft: true
showpagemeta: true
showcomments: true
---
```

If you want to hide something, try digging around your theme first and you might find a very easy way to do it! 

Also checkout the [page variables Hugo provides](https://gohugo.io/variables/page/).