---
title: The first few days of using Hugo for my Website
description: "Introducing my new personal homepage, but this time using Hugo and Render as opposed to the more traditional CMS-based systems like Wordpress. "
date: 2023-06-01T12:50:53.449Z
preview: ""
draft: false
tags:
  - hugo
  - render
  - Website
  - blog
categories:
  - This Website
keywords:
  - hugo
  - render
  - website
  - blog
---



## Reasoning

## Introducing Hugo

I'm late to the game, given Hugo has been around since 2016 ior so

Theme: [anake](https://themes.gohugo.io/themes/gohugo-theme-ananke/). This was the "default", or the theme used in the getting started documentation. I may keep using it for a while, I havent' really had time to look at other themes. It's a simple, clean theme. So no real complaints, yet.

### Hugo configuration

I made some basic configuration to the hugo configuration file `hugo.yaml`, I took a lot from the ananke theme's example file, but made the following changes:

```
baseURL = 'https://cdrum.com/'
Paginate = 9
enableRobotsTXT = true
enableGitInfo = true

[[params.ananke_socials]]
  // various social profiles...

[[menu.main]]
  name = 'About'
  pageRef = '/about'
  weight = 20

[[menu.main]]
  name = 'Now'
  pageRef = '/now'
  weight = 20
```

The `enableGitInfo` para is meant to get the lastmod working, but it's not. So something for me to investigate further.

## Places to host - Render

The reqirement was for free hosting with custom domain as I wanted `cdrum.com` to point to the site. Prior to this I was on a free wordpress instance, and they only provided a subdomain. Plus, it's wordpress. Yuck.

Prior to wordpress, I used blogger (again not on my own domain), and various VPS providers (Rackspace, AWS, to name a few). But since I don't want to be paying a monthly fee, I didn't want to fire up a VPS on AWS or someplace.

Enter **Render**.

Thankfully, [Render](https://render.com) offers free static site hosting xwith custom domains. And it connects to my github repo for automatic deployments once I commit (and push) updates.

(There are a [few other providers](https://gohugo.io/hosting-and-deployment/), but I chose Render on essentially a coin toss). Really, I think what I really liked was they provided CDN distribution, fully managed SSL and auto deplouments from Github. Ease is the primary requirement here.


## Tools

I am using [FrontMatter](https://frontmatter.codes), a headless CMS extension for Visual Studio Code that brings a bunch of the CMS functionality one would expect from a more traditional system like Wordpress.

### Frontmatter configuration

Some simple initial configuration in the `.vscode/settings.json:

```
   "frontMatter.content.pageFolders": [
        {
            "title": "posts",
            "path": "[[workspace]]/content/posts", 
            "filePrefix": "yyyyMMDD",
            "previewPath": "//"
        }
    ],
```

I'm a bit anal when it comes to file naming as I like the date in the filename. Easy to sort, etc.

## Conclusion

That's it for now. I have more work to do. But I'm opn vacation.