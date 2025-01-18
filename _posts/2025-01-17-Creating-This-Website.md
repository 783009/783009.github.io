---
title: "Getting started"
date: 2025-01-17
categories: [projects, Documentation, Tutorial]
tags: [Website, Chirpy, GitHubPages, Tutorial]
---
# Creating this website
This post will go through how I made this website step-by-step. MAKE SURE TO READ THE WHOLE SECTION BEFORE DOING ANYTHING.

## Create a repository
1. Sign in to GitHub and navigate to the **Chirpy Starter**.
2. Click the `Use this template` button and then select `Create a new repository`.
3. Name the new repository `<username>.github.io`, replacing `username` with your lowercase GitHub username.


## Configuration

Update the variables in `_config.yml` as needed. Some typical options include:

* `title` (line 17)
* `tagline` (line 19)
* `url` (line 26)
* `username` (line 29)
* `avatar` (line 101)
* `timezone` (line 12)
* `lang` (line 9)
* `email` (line 38)

When you are done click `Commit changes...`.

## Setting your avatar
To set your avatar which is the profile picture at the top of your navigation bar simply choose a folder such as `assets` and upload a square image into it and click `Commit changes...`. After that go to `_config.yml` line 101 and type `/YourFolderName/ImageName.ImageType`. For example `/assets/Lojayn.png`. Then click `Commit changes...`.

## About tab
To edit the the About tab on your website go to the `_tabs` folder and go into `about.md`. Proceed to removing the following lines.
``` Markdown
> Add Markdown syntax content to file `_tabs/about.md`{: .filepath } and it will show up on this page.
{: .prompt-tip }
```
After removing thoes lines you can write whatever you want using Markdown syntax(You can use this link as a guide <https://www.markdownguide.org/extended-syntax/>

## Making a post

There is a very thorough and detailed explanation on how to make a post created by the creator of this template at <https://chirpy.cotes.page/posts/write-a-new-post/>

### Video
This is the video I used while making this website however I did not use jekyll or VS code i only used it to learn how to start up my website.
