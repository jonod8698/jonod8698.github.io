---
title: "Hugo"
description: "Hugo"
date: 2021-12-27T22:41:38+11:00
lastmod: 2021-12-27T22:41:38+11:00
draft: false

author: "JD"
authorLink: ""

tags: [knowledge-management,static-site]
categories: [other]
featuredImage: ""
featuredImagePreview: ""

hiddenFromHomePage: false
hiddenFromSearch: false

toc:
    enable: true
    auto: true

comment:
    enable: true

seo:
    images:
---

Like many others, I'm sick of using Wordpress. I've finally decided to switch to a static site using [Hugo](https://gohugo.io/).

## Hugo Setup

### Install Hugo

I'm using the [DoIT](https://github.com/HEIGE-PCloud/DoIt.git) Hugo theme. This requires [Hugo Extended](https://community.chocolatey.org/packages/hugo-extended).

**Installation via Chocolatey**
```powershell
choco install hugo-extended
```

### Create a new site

Hugo will create a new site named `<my-site>`
```powershell
hugo new site <my-site>
```

### Install a theme
I like the simplicity and aesthetics of the [DoIT](https://codeit.suntprogramator.dev/) theme.

Create a git repository and make the CodeIT repo a submodule of the side directory.
```powershell
cd <my-site>
git init
git submodule add https://github.com/HEIGE-PCloud/DoIt.git themes/DoIT
```

### Configure the site



In the root of the site, Hugo will create a [`config.toml`](https://gohugo.io/getting-started/configuration/) file. Here is an example:
```markdown
contentDir = "content"
layoutDir = "layouts"
publishDir = "public"
buildDrafts = false
baseURL = "https://yoursite.example.com/"
canonifyURLs = true
title = "My Hugo Site"

[taxonomies]
  category = "categories"
  tag = "tags"

[params]
  subtitle = "Hugo is Absurdly Fast!"
  author = "John Doe"
```
### Create a new post

Hugo will create a new post in the `content\posts` directory.

All posts are formatted in Markdown.

```powershell
hugo new posts/my-post.md
```

### Run Local Dev Server

```powershell
hugo serve
```

## Deploy to Github Pages
Follow the [instructions](https://pages.github.com/) here to create a new Github Pages site.

## Automate Deployment with Github Actions
Follow the [instructions](https://gohugo.io/hosting-and-deployment/hosting-on-github/#build-hugo-with-github-action) here to set up a Github Action to build and deploy Hugo sites.