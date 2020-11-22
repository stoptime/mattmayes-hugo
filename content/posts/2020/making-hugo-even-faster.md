---
title: "Making Hugo Even Faster"
date: 2020-01-04T17:03:26-06:00
tags : [ "dev", "hugo", "performance" ]
draft: false
showpagemeta: true
showcomments: true
---

> **UPDATE** this site is now on [Netlify](https://www.netlify.com/), which is _frickin' awesome (and free for basic sites)_ but if you're on another host, especially one that uses Apache, this might help you.

We all want our mobile [PageSpeed Insights](https://developers.google.com/speed/pagespeed/insights/) score to be in the 90s right? There's more to it than tossing on that old flannel, here are some things that helped me.

## Take advantage of Free Cloudflare
[Cloudflare](https://www.cloudflare.com/) has a free plan that can really boost your site by handily taking care of things like minification, serving your site over HTTP/2 or even HTTP/3 when possible, "Page Rules" you can use to cache the heck out of those static assets, Brotli compression (the safe version of gzip for https connections); their Rocket Loader&trade; improves paint time for pages that include javascript - not to mention their free and easy HTTPS and everything is on their CDN. This is a no brainer.

## Minimize your css/js/html
Hugo kindly does this for us - thanks Hugo! When you build just add the minify tag: `hugo --minify`.

## Inline and combine your css/js when possible
This really made a difference for me - if your css/js is small enough (I _think_ around 10kb is small enough) why not inline it? You can probably  minify your print css too if you have it: 
```go
{{ $options := (dict "outputStyle" "compressed" "enableSourceMap" true) }}
{{ $optionsInline := (dict "outputStyle" "compressed") }}

{{ with resources.Get "scss/hyde-hyde-mateo.scss" | toCSS $optionsInline }}
<style type="text/css" media="screen">{{ .Content | safeCSS }}</style>
{{ end }}

{{ with resources.Get "scss/print.scss" | toCSS $optionsInline }}
<style type="text/css" media="print">{{ .Content | safeCSS }}</style>
{{ end }}
```

### Do you really need those icons/webfonts/etc? 
I know, sometimes you do! If so, make sure to prefetch it's dns - it helps. For example:
```
<link rel="preconnect dns-prefetch" href="https://use.fontawesome.com">
```

### Google Analytics will chop your score by 10-20 points
I know right? Ridiculous. I don't implement it like they tell you to: by making it the first thing in the `<head>` - rather, I put it in the footer, and do the dns prefetch trick up in the header, which all helps:
```
<link rel="preconnect dns-prefetch" href="https://www.google-analytics.com">
```

Note Google has been making updates, so you may also need:
```
<link rel="preconnect dns-prefetch" href="https://www.googletagmanager.com">
```

Check your provided script/resources. 

### Images
LazyLoad images with something like [Lazysizes](https://github.com/aFarkas/lazysizes) - and use [webp](https://www.google.com/search?q=webp+images&oq=webp+images&aqs=chrome..69i57j0j69i60l6.2262j0j7&sourceid=chrome&ie=UTF-8) where possible. If you're on a 'nix machine, you may already have the [webp generator installed as a command line tool](https://web.dev/codelab-serve-images-webp/).

### Apache caching
Using Apache? Set some cache headers in your `.htaccess` file:
```apache
<ifModule mod_expires.c>  
  ExpiresActive On  
  ExpiresDefault "access plus 5 seconds"  
  ExpiresByType image/x-icon "access plus 2592000 seconds"  
  ExpiresByType image/jpeg "access plus 2592000 seconds"  
  ExpiresByType image/png "access plus 2592000 seconds"  
  ExpiresByType image/gif "access plus 2592000 seconds"
  ExpiresByType image/webp "access plus 2592000 seconds" 
  ExpiresByType image/svg+xml "access plus 2592000 seconds"
  ExpiresByType application/x-font-ttf "access plus 2592000 seconds"
  ExpiresByType application/x-font-truetype "access plus 2592000 seconds"
  ExpiresByType application/x-font-opentype "access plus 2592000 seconds"
  ExpiresByType application/x-font-woff "access plus 2592000 seconds"
  ExpiresByType application/font-woff2 "access plus 2592000 seconds"
  ExpiresByType application/vnd.ms-fontobject "access plus 2592000 seconds"
  ExpiresByType application/font-sfnt "access plus 2592000 seconds"
  ExpiresByType text/css "access plus 604800 seconds"  
  ExpiresByType text/javascript "access plus 216000 seconds"  
  ExpiresByType application/javascript "access plus 216000 seconds"  
  ExpiresByType application/x-javascript "access plus 216000 seconds"   
</ifModule> 
```

#### In closing
You're taking to the time to learn about and use a static site generator, might as well get the most out of it 🚀
