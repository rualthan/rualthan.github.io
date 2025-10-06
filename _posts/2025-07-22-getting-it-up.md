---
layout: post
title: Getting it up!
---

This is my second attempt at blogging with Github pages and Jekyll. It took more effort than I expected to set it up.

1. You need a Github account. I gave got one.
2. I forked [jekyll-now](jekyll-now) to yourGithubusername.github.io
3. In the forked repo, I updated _config.yml to trigger a build.
4. I saw the build status under Deployments on the right side.
5. Once the build is done, in your repo > Settings > Pages, you should see "Your site is live at https://yourGithubusername.github.io"
6. Congrats! Your Github Page is live. 

The main purpose of blogging on Github Pages is to get git workflows into my muscle memory. This means composing blog posts locally and use git to publish it. 

I tried following the [Local Development](https://github.com/barryclark/jekyll-now?tab=readme-ov-file#local-development) instructions on the source repo. I got stuck trying to install github-pages "gem install github-pages". This was resolved by installing the latest stable Ruby. My OS has 2.6 version whereas 2.7 or higher is required by github-pages. This too took some effort as I am not familiar with Ruby and rbenv etc.  

With Jekyll installed, I locally cloned my repository. I could see a local version at [http://127.0.0.1:4000/]( http://127.0.0.1:4000/) by running `jekyll serve` from the repo directory in terminal.

I created a post in Markdown at _posts/YYYY-MM-DD-title.md.

After this, it's git add., git commit -m "add first post" and git push. Voila, my first post is live.