---
title: "Set up and work locally"
linkTitle: "Set up and work locally"
weight: 3
date: 2019-07-09
description: >
  How to set up and build this documentation site locally.
---

If you are a maintainer of this documentation site, or a contributor who will be making frequent or major contributions to the documentation, we recommend you set up and build the site locally on your computer. This enables you to easily test changes locally before pushing to Github.

## Before you start

* You will need a Git client installed, such as Gitbash
* You will need write access to the Git repo

## Clone the Git repo

Clone the git repo. Don't forget to use `--recurse-submodules` or you won't pull down some of the code you need to generate a working site.

```
git clone --recurse-submodules --depth 1 https://github.com/alexearnshaw/improve-the-docs.git
```

## Install Hugo

You need a [recent version](https://github.com/gohugoio/hugo/releases) of Hugo to build and run the site. If you install from the release page, make sure to get the `extended` Hugo version, which supports SCSS: you may need to scroll down the list of releases. Hugo can be installed via Brew if you're running MacOs. If you're a Linux user, do not use `sudo apt-get install hugo`, as it currently doesn't get you the `extended` version.

For full installation instructions for each platform, see [Install Hugo](https://gohugo.io/getting-started/installing/).

## Install theme dependencies

The theme uses `PostCSS` to generate the site resources the first time you run the server. Install it using `npm`:

```
npm install -D --save autoprefixer
npm install -D --save postcss-cli
```

## Build the site locally

Run the `hugo server` command in your site root.

The website is now available locally at `http://localhost:1313/`. You can now add or edit Markdown files in the `content\en\docs\` directory and Hugo will automatically rebuild the site with the changes. 

For more details on Hugo commands, see [Basic Usage](https://gohugo.io/getting-started/usage/).
