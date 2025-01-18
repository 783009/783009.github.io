---
title: "Getting started"
date: 2025-01-18
categories: [projects, Documentation, Turtorial]
tags: [Website, Chirpy, GitHub Pages, Tutorial]
---
# Creating this website
This post will go through how I made this website step-by-step. MAKE SURE TO READ THE WHOLE SECTION BEFORE DOING ANYTHING.

## Create a repository
The first step is to create a repository using a theme or template. I went with the Chirpy starter template by cotes2020. To use the template simply click the "Use this template"(near the top right corner of the page) button and then click "Create a new repository". From there you will set the repository name to `YourUsername.github.io` and make sure the repository is set to public.
## Editing your website
The next step is to configure your website. You can do this from `_config.yml`. You should be able to find it near the botton of the files within your repository. From here there are a few things to change. To start off set your `timezone`(line 12) to `Pacific/US`. To change the title simply edit the text written after `title`(line 17) and to edit the text that appears underneath the title change the `tagline` (line 19). To make your website visible update the `URL`(line 26) by setting it to `https://YourUsername.github.io`. Also set `username`(line 29) to "YourUsername". Set `name` to "Your Name" and `email`(line 38) to "Youremail@gmail.com". When you are done click "Commit changes..."
## Setting your avatar
To set your avatar which is the profile picture at the top of your navigation bar simply choose a folder such as `assets` and upload an image into it and click "Commit changes...". After that go to `_config.yml` line 101 and type `/YourFolderName/ImageName.ImageType`. For example `/assets/Lojayn.png`. Then click "Commit changes...".
## About tab
To edit the the About tab on your website go to the `_tabs` folder and go into `about.md`. Proceed to removing the following lines.
```Markdown
> Add Markdown syntax content to file `_tabs/about.md`{: .filepath } and it will show up on this page.
{: .prompt-tip }
```
After removing thoes lines you can write whatever you want using Markdown syntax(You can use this link as a guide <https://www.markdownguide.org/extended-syntax/>)
