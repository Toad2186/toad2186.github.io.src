+++
date = "2016-07-24"
title = "Hello World!"
tags = [
    "go",
    "golang",
    "hugo",
    "blog",
    "github",
]
categories = [
    "meta",
    "blog",
    "github"
]
author = "toan vuong"
description = "I've been meaning to do this for a while but the planets have never"
+++

### **Introduction**
I've been meaning to do this for a while but the planets have never aligned properly until today. This website will server as a dump for my thoughts which will mostly cover various software engineering related topics with occasional random thoughts in between. Whatever comes to my mind that I think is important to be recorded.

### **Hugo**
[Hugo](https://gohugo.io/) is a static website generator. Given a pre-defined set of constraints and configurations and a set of inputs (In this case in [toml](https://github.com/toml-lang/toml) and [markdown](https://daringfireball.net/projects/markdown/) format), static website generators will generate a website for you, converting your inputs into static html files which can then be served using a webserver such as [apache](https://httpd.apache.org/) or [nginx](https://www.nginx.com/resources/wiki/). 

### **Why Hugo?**
Luck. In choosing Hugo, I started out by Googling for static website generators, which led me to a whole slew of them listed on a certain [website](https://www.staticgen.com/). From there, I merely clicked on one semi-randomly. I didn't care much about compilation speed since I didn't think I'd have a whole lot of content that such a thing would matter. I _did_ care that it should be simple to use, as I didn't want the tool to be in control and Hugo's documentation advertises a new hugo site in 2 under minutes!

As it turns out, that number is fairly accurate. I grabbed the most recent [release](https://github.com/spf13/hugo/releases/tag/v0.16), installed the Debian package on my Ubuntu 16.04 VM:
```
wget https://github.com/spf13/hugo/releases/download/v0.16/hugo_0.16-1_amd64.deb
sudo dpkg -i hugo_0.16-1_amd64.deb
```

Instructions for other platforms can be found on Hugo's website.

Installing hugo gives us the hugo binary, and things are fairly straight forward from here so I won't beat the deadhorse. This [quickstart](https://gohugo.io/overview/quickstart/) guide should be all you need!

The biggest annoyance I found was that my development environment wasn't on localhost and thus a few special parameters to hugo was required. My full debug command:
```
 hugo server --bind "192.168.1.121" --baseURL "http://192.168.1.121/" --buildDrafts --theme=hugo-steam-theme
# --bind Binds to a specific IP address
# --baseURL I assume determines the HTTP Host header that our hugo HTTP server accepts. By default this is localhost and will not work if the development environment isn't localhost, even if one sets the bind address differently
 ```
 Personally I think hugo should be changed so that `--baseURL` isn't necessary. `hugo` doesn't seem to allow a single process to host multiple sites (virtual hosts) so there's no point in requiring this parameter.

### **Steam**
I chose [steam](http://themes.gohugo.io/theme/steam/post/creating-a-new-theme/) for its simplicity. Being new to Hugo, it took a little while to get everything running smoothly. The biggest thing here is to be sure to follow the theme's installation instructions and look at the `hugo-steam-theme/exampleSite` directory for usage examples.

Another nice thing about steam is the ability to have custom pages with links. So this website can be used as a multi-purpose site rather than a blog-only page!


### **Hosting**
I'm not a stranger to hosting services--I have two VPSes, a Digital Ocean Droplet, and I've also used S3/Cloudfront at work. What I cared most about here was simplicity in hosting. I opted to use [Github Pages](https://pages.github.com/) for this task. Let me preface by saying that this isn't necessary, and by default if you name your repo `<github_handle>.github.io` then the whole repo will be served out of the box without any issues. So by this point, http://toad2186.github.io is already accessible. What I wanted was for http://toanvuong.io to be accessible and serving the same

This, in itself, wasn't too difficult as I'm somewhat familiar with DNS. Basically you point the DNS for your domain to point to various Github servers. This can either be your apex domain (e.g. `toanvuong.io`) or www (e.g. `www.toanvuong.io`) domain. Github has a great [guide](https://help.github.com/articles/using-a-custom-domain-with-github-pages/) on how to do this.

Note that Github will serve your whole directory, and due to the way hugo works,
```
hugo --theme=hugo-steam-theme
```
generates your website under the `public` directory. So you'll end up having to go to `toanvuong.io/public/`, and even then, this won't work due to the way `hugo` generates links: namely, `hugo` generates [absolute](http://www.coffeecup.com/help/articles/absolute-vs-relative-pathslinks/) links. This means that resources will be requested from `http://toanvuong.io/js/...` for example, which is all wrong.

To get around this, I have two git repos. My main (source) repo is at `https://github.com/Toad2186/toad2186.github.io.src` and I've created `https://github.com/Toad2186/toad2186.github.io` just for the `public` directory. Simple, but works. Shoot my a message if you've got a better idea! Quite frankly, I'm surprised Github doesn't allow one to customize the root directory--seems like it'd be a frequently requested feature given that static website generators would probably create these "build" directories as an output! Perhaps this has something to do with their integration with [Jekyll](https://help.github.com/articles/using-jekyll-as-a-static-site-generator-with-github-pages/). This brings me to my next point, we'd need to add a `.nojekyll` (which can be an empty file) to the public folder so that github ignores its automatic jekyll build.

My last gripe with Github hosting is the lack of https which is sensible given the constraints. Unless they do some man-in-the-middle decryption/encryption or have an option for users to upload SSL certificates for their personal domains, it's not possible for Github to provide HTTPS hosting. Furthermore, virtual hosts (which is what I assume Github uses) gets complicated with HTTPS since the HTTP Host header is no longer readable--A [TLS Server Name Indication extension](https://www.digitalocean.com/community/tutorials/how-to-set-up-multiple-ssl-certificates-on-one-ip-with-apache-on-ubuntu-12-04)is required for such a feat. So it's possible, but difficult. For now though, since there isn't any sensitive information flowing through these pages, http should be fine :).

All in all, it was rather simple and saved me the need to setup nginx on one of my existing virtual servers (or worse, spin up a new VPS!). Github it is, until I find a better host!
