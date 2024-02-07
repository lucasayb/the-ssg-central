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

Creating a file collection

On my particular Hugo theme, I have a file located in
