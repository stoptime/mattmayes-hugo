---
title: "Hugo: How to Properly Add Custom CSS"
date: 2019-12-16T18:01:33-06:00
tags : [ "dev", "hugo", "go" ]
---

So you've picked out and installed your [Hugo](https://gohugo.io/) theme, but you just need to tweak a few things here and there, yet you don't see a config setting that allows you add your own css file(s) in config.toml 👎 

In addition to adding your own css file, wouldn't it be great if they were minimized and had the [integrity](https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity) set too? 

> Just to note, I'm using the latest version of the [hyde-hyde](https://themes.gohugo.io/hyde-hyde/) theme as an example, and Hugo version v0.61.0/extended.

First thing you need to do is dive into your theme, and figure out what layout file is responsible for outputting the css links. In hyde-hyde, that's:

`my-site/themes/hyde-hyde/layouts/partials/header/styles.html`

Now, in your "regular" non-theme directory, you need to recreate that exact path and file - making directories if you need to:

`my-site/layouts/partials/header/styles.html`

And add the following highlighted lines to it:

```go {linenos=table,hl_lines=[4, "12-15"]}
{{ partial "header/styles-highlight.html" . }}

{{ $options := (dict "outputStyle" "compressed" "enableSourceMap" true) }}
{{ $optionsCss := (dict "outputStyle" "compressed") }}

{{ $style := resources.Get "scss/hyde-hyde.scss" | toCSS $options | fingerprint }}
<link rel="stylesheet" href="{{ $style.RelPermalink }}" integrity="{{ $style.Data.Integrity }}">

{{ $printStyle := resources.Get "scss/print.scss" | toCSS $options | fingerprint }}
<link rel="stylesheet" href="{{ $printStyle.RelPermalink }}" integrity="{{ $printStyle.Data.Integrity }}" media="print">

{{ range .Site.Params.customCss -}}
{{ $customStyle := resources.Get . | toCSS $optionsCss | fingerprint }}
<link rel="stylesheet" href="{{ $customStyle.RelPermalink }}" integrity="{{ $customStyle.Data.Integrity }}">
{{- end }}
```

You may notice we are iterating over a range of values - this means we can have more than 1 custom css file :)

Now we need to tell our config file about it, so in your config.toml we add:

```toml
## Site Settings
[params]
    customCss = ["css/custom.css"]
```

I am only add one here, but you can added more like `["css/foo.css", "css/bar.css"]` or whatever.

Now, the trick with `resources.Get` is that it seems to only look in folders named "assets" - so make this:

`my-site/assets/css/custom.css`

Now you should be good to go! Just add more files as needed. 

### What I do NOT know
Is how to get this bundled into the main scss/css compiling, so we only have a single, minified css file...