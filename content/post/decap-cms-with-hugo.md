---
title: "Decap CMS with Hugo"
description: "Let's integrate the Open Source CMS with Hugo with just a few tips"
date: 2024-02-05T17:15:30-03:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
---

Before anything, I'm assuming that you have the following stack:
- Hugo extended ~>0.113.0
- Github as the cloud-based Git repository manager
- Your site deployed into Netlify or Vercel
- A little bit of experience with Git

## 1. Create a `config.yml` file into the `static/admin` folder.

In this file, you should place the following content:

```yaml
backend:
  name: git
  branch: main # Use your default branch here

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

Let's see what it's happening in this code:
```yaml
backend:
  name: git
  branch: main # Use your default branch here
```
We need to define where the content of the website is coming from. That's way we define that we handling with Git and the branch is the `main`. Keep in mind that there a few themes that still creates the branch as `master`. This is not a good practice, but still happens. Change it in the way it suits your case.

```yaml
media_folder: "static/uploads" # Adjust based on your media storage location
public_folder: "/uploads" # URL path for accessing media
```
With Decap CMS, we can upload media. the `media_folder` is where the Decap CMS is going to send these media. The `public_folder` is the URL that is used to access these media. If you change any of those folders, you should change both for the same name. For example: 

```yaml
media_folder: "static/cool_images" # Adjust based on your media storage location
public_folder: "/cool_images" # URL path for accessing media
```

```yaml
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

This is where things get trick. A collection is basically the folder where you store your posts. Here you can have the collections based on  your needs. But I'm going to exemplify using the `posts` collection

```yaml
  - name: "posts" # This is the name used in the URL
```
The name `posts` is the URL that is going to be displayed into the CMS.

```yaml
    label: "Post" # This is the singular name of the content type for the UI
```
In the UI, every element that refers to a post, is going to have the label that you define in here.

```yaml
    folder: "content/post" # The folder where your posts markdown files will be saved
```
By default, a post in Hugo is stored in the `content/posts` folder. In my case, I'll use the `content/post` because it is the way my theme handles it.

```yaml
    create: true # Allows users to create new documents in this collection
```
Keep this option as `true`. This tells the CMS that a user can create a post using the CMS, otherwise you would be able only to edit or view the posts.

```yaml
    slug: "{{year}}-{{month}}-{{day}}-{{slug}}" # Filename template for new posts
```
As Hugo is based on files, the filename has a great importante into how your data is stored and how they are viewed in the public perspective. In this line, we are telling to Decap CMS how we want it to create the file name of each of our posts. But I'll recommend for you to keep in the way above, because this way works fine.

```yaml
    fields: # List of fields for the front matter
      - { label: "Title", name: "title", widget: "string" }
      - { label: "Description", name: "description", widget: "text" }
      - { label: "Date", name: "date", widget: "datetime" }
      - { label: "Image", name: "image", widget: "image", required: false }
      - { label: "Hidden", name: "hidden", widget: "boolean", default: false }
      - { label: "Comments", name: "comments", widget: "boolean", default: true }
      - { label: "Draft", name: "draft", widget: "boolean", default: true }
      - { label: "Weight", name: "weight", widget: "number" }
```

Here we have all the fields that comes by default with Hugo whenever we create a new post with `hugo new content` command. Feel free to change the way you prefer. For example if you don't want to show the Draft field and wants it to always to come marked as `true`, you can change the draft field to the following:

```yaml
      - { label: "Draft", name: "draft", widget: "hidden", default: false }
```

This will not show the field in the CMS and every time that you create a new post through it, the post will never be marked as draft. This is handy when you are a solo blogger and don't want to worry to much with editorial workflow.

## 2. Creating the `index.html` file into the `static/admin` folder.

This step if pretty straightforward. All you need to do is to create the file as mentioned and place the following content:
```html
<!doctype html>
<html>
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <meta name="robots" content="noindex" />
  <title>Content Manager</title>
</head>
<body>
  <!-- Include the script that builds the page and powers Decap CMS -->
  <script src="https://unpkg.com/decap-cms@^3.0.0/dist/decap-cms.js"></script>
</body>
</html>
```
That's basically it. Now, there are possible customizations that can be done into this file, like creating custom elements into the dashboard and modifying the previews, but I'm not going to cover them in this post.

## 3. Deployment of these files

After all that, you can just commit both of this files and push it to your repository. You can either use the Ui for that or the command line
```bash
$ 
```

