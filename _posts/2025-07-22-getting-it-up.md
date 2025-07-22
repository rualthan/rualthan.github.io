---
layout: post
title: Getting it up!
---

This is my second attempt at blogging with Github pages and Jekyll. It took more effort than I expected to set it up.

1. You need a Github account. I gave got one.
2. Clone [jekyll-now](jekyll-now) to yourGithubusername.github.io
3. In your yourGithubusername.github.io repo, update _config.yml to trigger a build.
4. You can see the build status under Deployments on the right side.
5. Once the build is done, in your repo > Settings > Pages, you should see "Your site is live at https://yourGithubusername.github.io"
6. Congrats! Your Github Page is live. 

The main purpose of blogging on Github Pages is to get git workflows into my muscle memory. This means composing blog posts locally and use git to publish it. 

I tried following the [Local Development](https://github.com/barryclark/jekyll-now?tab=readme-ov-file#local-development) instructions on the source repo. I ran into issues at the first step as I am unfamiliar with install Ruby packages and resolving conflict. 

1. My OS (macOS Sequoia v15.5) has Ruby 2.6x installed whereas github-pages requires 2.7 or later.
2. I installed the latest stable Ruby (3.3.6)
3. Installed Jekyll and plygins sudo gem install github-pages -V
4. Cloned my repo `git clone https://github.com/yourusername/yourusername.github.io.git`
5. `cd yourusername.github.io.git`
6. `jekyll serve`
7. View the local version at  [http://127.0.0.1:4000/]( http://127.0.0.1:4000/)
8. git add . , git commit -m "add new post: Getting it up!", git push
9. The first post is published with git workflow.

To install the latest Ruby, I did the following:

```brew install rbenv;rbenv install 3.3.3;rbenv global 3.2.2```


After re-opening the terminal, ruby -v still shows 2.6. I added the following to my .zprofile file.

```
export PATH="$HOME/.rbenv/bin:$PATH"
eval "$(rbenv init - zsh)"
```

After re-opening the terminal, ruby -v returns the expected version.
