# Corporal Bugz - GitHub Pages site

Static/Jekyll replacement for the Wix-hosted Corporal Bugz blog.

## Publish on GitHub Pages

1. Create a new GitHub repository (for example `corporalbugz`).
2. Push the contents of this folder to the repository's `main` branch.
3. In **Settings → Pages**, choose **Deploy from a branch** and select `main` / `(root)`.
4. Add `www.corporalbugz.com` as the custom domain in GitHub Pages.
5. Only after the GitHub preview works, update DNS at the domain provider.

GitHub Pages will build the site using Jekyll automatically.

## Add a new blog post

Create a Markdown file under `_posts` using this naming convention:

`YYYY-MM-DD-my-post-title.md`

Example front matter:

```yaml
---
layout: post
title: "My post title"
date: 2026-08-14
permalink: /post/my-post-title/
hero: /assets/img/posts/my-image.jpg
tags: [Active Directory, Red Teaming]
read_time: 5
excerpt_text: "Short description used on blog cards."
---
```

Then write the post in normal Markdown. Commit and push; GitHub Pages rebuilds automatically.

## Existing Wix URLs preserved

All six current posts use the same `/post/.../` slugs as the Wix site, which avoids breaking existing links after the DNS cutover.
