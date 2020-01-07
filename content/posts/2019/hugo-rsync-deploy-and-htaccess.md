---
title: "Hugo Rsync Deploy and Htaccess"
date: 2019-12-18T17:47:25-06:00
tags : [ "dev", "hugo" ]
showpagemeta: true
showcomments: true
---

Deploying this site to my [WebFaction](https://www.webfaction.com/?aid=30691) account was super easy, I pretty much just followed the [Hugo docs for rsync deployment](https://gohugo.io/hosting-and-deployment/deployment-with-rsync/), with one minor change to minify the html output:

```sh
#!/bin/sh

USER=your-username
HOST=your-username.webfactional.com             
DIR=webapps/your-app/   # the directory where your web site files should go

hugo --minify && rsync -avz --delete public/ ${USER}@${HOST}:~/${DIR}

exit 0
```

Note the `--minify` flag after `hugo`, which will make your pages even more performant by minifying the the html output.

You might be wondering what the `--delete` flag does. That an [rsync](https://linux.die.net/man/1/rsync) option that removes any unrelated project files from the _destination_ directory of your host. So heads up any non-project/hugo-related file will be deleted in your host directory.

## .htaccess file and 404 pages

At WebFaction you can setup a "static" app - a barebones Apache app meant for speed. This sounded like a nice option for Hugo. However, the tradeoff is that .htaccess files won't get picked up. I really like friendly 404 pages, so I chose to use a regular Apache app with the typical mods loaded, and I can now redirect 404's like this in .htaccess:

```ErrorDocument 404 /404.html```

[Instruction on other web servers here](https://gohugo.io/templates/404/).

The one thing that tripped me up was that you need to make sure you put the .htaccess file in your `static` directory, otherwise Hugo will delete it if it's in your root directory - I noticed this on my first deploy:

```sh
building file list ... done
deleting .htaccess
```

Once I moved it into the static dir it was picked up, deployed and working. Lastly...

### Disqus won't work in your local environment 

If you use Disqus for comments - they won't work unless you're on your production site. Just FYI there.

Cheers and happy Hugo'ing! So far I'm enjoying the ride 🍻

#### 1/1/2020 Update
I ditched Disqus after noticing all the crap it injects - my performance scores immediately went up. Uncertain if I need a commenting platform or not, if you have an opinion please [let me know](/contact).

I've also taken advantage of [Cloudflare's free plan.](https://www.cloudflare.com/plans/) for a little extra boost.

I've also added some caching directives, so my .htaccess now looks like this (may still tweak the cache lengths):

```apache
ErrorDocument 404 /404.html

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