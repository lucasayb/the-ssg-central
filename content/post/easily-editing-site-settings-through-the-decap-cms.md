---
title: Easily editing site settings through the Decap CMS
description: Change the site name, social networks and more using the power of
  the Decap CMS (formerly Netlify CMS)
date: 2024-02-07T03:47:34.272Z
hidden: false
comments: true
draft: false
categories:
  - Headless CMS
tags:
  - Decap CMS
---
While building this site, I often find changing some of its configurations necessary. As I'm working with an SSG, most of these settings must be done with Git and Github. Although I'm a developer and use Git and GitHub every day, this might be a little unpractical if you are not working on a computer, for example, and need to change through your smartphone. And I thought to myself: if only there were a way to change these settings through the Decap CMS.

Well, there is. And it's quite easy. 

But before explaining how to do it, I need to explain some topics to you.

## Folder Collections vs Files Collections

Recently, I wrote an article showing [how we could integrate the Hugo SSG with the Decap CMS](https://thessgcentral.com/p/decap-cms-with-hugo-in-netlify/). In the end, we build this `config.yml`, saved in the `static/admin` of my Hugo theme:

```yaml
backend:
  name: github
  repo: lucasayb/the-ssg-central
  branch: main
  site_domain: thessgcentral.com

media_folder: "static/uploads" # Adjust based on your media storage location
public_folder: "/uploads" # URL path for accessing media

collections:
  - name: "posts" # This is the name used in the URL
    label: "Post" # This is the singular name of the content type for the UI
    folder: "content/post" # The folder where your posts markdown files will be saved
    create: true # Allows users to create new documents in this collection
    slug: "{{year}}-{{month}}-{{day}}-{{slug}}" # Filename template for new posts
    fields: # List of fields for the front matter
      - { label: "Title", name: "title", widget: "string" }
      - { label: "Description", name: "description", widget: "text" }
      - { label: "Date", name: "date", widget: "datetime" }
      - { label: "Image", name: "image", widget: "image", required: false }
      - { label: "Hidden", name: "hidden", widget: "boolean", default: false }
      - { label: "Comments", name: "comments", widget: "boolean", default: true }
      - { label: "Draft", name: "draft", widget: "boolean", default: false }
      - { label: "Weight", name: "weight", widget: "number" }
      - { label: "Body", name: "body", widget: "markdown" }
```

What we've done here is build the file for the basic configuration of the Decap CMS. When we have a folder like `_posts` in Jekyll or `content/posts` (or `content/post` depending on your theme) in Hugo, we can have one or many markdown files inside the folder, and we want to add, edit, or remove the articles inside it through the Decap CMS. And when we do that, we are using a folder collection. You can have folder collections to manage posts, categories, and tags, and inside each folder, you will have many markdown files, and everything will be editable through the CMS.

A file collection is slightly different. This is a `_config.yml` file to edit a Jekyll website:

```yaml
title: Lucas Yamamoto
email: lucasayb97@gmail.com
description: >- # this means to ignore newlines until "baseurl:"
  My cool description
baseurl: ""
url: "https://www.lucasyamamoto.com" 
twitter_username: lucasayb
github_username:  lucasayb
linkedin_username:  lucasayb
instagram_username:  lucasyamamoto1997

disqus:
  shortname: lucas-yamamoto
show_excerpts: true

theme: minima
plugins:
  - jekyll-redirect-from
  - jekyll-feed
minima:
  skin: dark
  date_format: "%b %-d, %Y"
```

Using the file collection, I can edit this single file without adding a new one or removing this existing one through a section in my Decap CMS. This allows us to change the Disqus short name, the site title, description, social networks, and everything that can be edited through this file.

## Creating a file collection

On my particular Hugo theme, I have a file located in `config/_default/config.toml`, with the following content:

```toml
# Change baseurl before deploy
baseurl = "https://www.thessgcentral.com"
languageCode = "en-us"
paginate = 5
title = "The SSG Central"

# Theme i18n support
# Available values: en, fr, id, ja, ko, pt-br, zh-cn, zh-tw, es, de, nl, it, th, el, uk, ar
defaultContentLanguage = "en"

# Set hasCJKLanguage to true if DefaultContentLanguage is in [zh-cn ja ko]
# This will make .Summary and .WordCount behave correctly for CJK languages.
hasCJKLanguage = false

# Change it to your Disqus shortname before using
disqusShortname = "thessgcentral"
```

Let's say that I want to have the power to change the `disqusShortname` of the site through the Decap CMS. We can just change the file that we built so far to have the following:

```yaml
backend:
  name: github
  repo: lucasayb/the-ssg-central
  branch: main
  site_domain: thessgcentral.com

media_folder: "static/uploads" # Adjust based on your media storage location
public_folder: "/uploads" # URL path for accessing media

collections:
  - name: "posts" # This is the name used in the URL
    label: "Post" # This is the singular name of the content type for the UI
    folder: "content/post" # The folder where your posts markdown files will be saved
    create: true # Allows users to create new documents in this collection
    slug: "{{year}}-{{month}}-{{day}}-{{slug}}" # Filename template for new posts
    fields: # List of fields for the front matter
      - { label: "Title", name: "title", widget: "string" }
      - { label: "Description", name: "description", widget: "text" }
      - { label: "Date", name: "date", widget: "datetime" }
      - { label: "Image", name: "image", widget: "image", required: false }
      - { label: "Hidden", name: "hidden", widget: "boolean", default: false }
      - { label: "Comments", name: "comments", widget: "boolean", default: true }
      - { label: "Draft", name: "draft", widget: "boolean", default: false }
      - { label: "Weight", name: "weight", widget: "number" }
      - { label: "Body", name: "body", widget: "markdown" }

  - label: "Site Settings"
    name: "settings"
    files:
      - label: "General settings"
        name: "config"
        editor:
          preview: false
        file: "config/_default/config.toml"
        fields: # List of fields for the category front matter
        - {label: "Disqus Shortname", name: "disqusShortname", widget: "string"}
```

Saving this file into the `static/admin/config.yml`, you have two options:

You can either test locally (depending on what SSG you are using) or you can commit and push to Github to see the settings in your admin with the following command:

```
$ git add .
$ git commit -m "adds the file collection for the settings"
$ git push -u origin main

```

We can wait for the build time of our site and then, accessing the Decap CMS, we can see that a new section has appeared in the sidebar:

![](/uploads/scr-20240207-bkax.png)

Clicking on Site settings, we can see that there is  another section called General settings and after entering it, we have the following view:

![](/uploads/scr-20240207-bkoq.png)

We can then edit our Disqus Shortname through the panel.

Now let's understand the code that we implemented:

```yaml
  - label: "Site Settings"
    name: "settings"
```

If you already realized, `label` is the name that appears in the UI. `name` is actually how this collection is visualized through the URL of the CMS.

```yaml
    files:
```

This line just defines that we are working with file collection. We can define as many files as we want, as it's a list of files, not a single one. This is useful when your site's configurations is divided into many files

```yaml
      - label: "General settings"
        name: "config"
```

These are, once again, the `label`, the UI name of the settings that we are working on, and the `name`, the parameter that appears in the URL.

```yaml
        editor:
          preview: false
         
```

There is no need to see in real-time in the preview panel what changes we are doing.

```yaml
        file: "config/_default/config.toml"
```

You saw before that we mentioned that we could edit the _config.yml, a YAML file, of a Jekyll theme. And if you see carefully, this is actually a TOML file. Turns out that Decap CMS accepts a lot of extensions. They can all be found in [here](https://decapcms.org/docs/configuration-options/).
