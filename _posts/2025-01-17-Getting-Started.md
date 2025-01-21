---
title: "Getting started"
date: 2025-01-17
categories: [projects, Documentation, Tutorial]
tags: [Website, Chirpy, GitHub Pages, Tutorial]
---
# Getting started
This post will go through how I made this website step-by-step. MAKE SURE TO READ THE WHOLE SECTION BEFORE DOING ANYTHING.

## Create a repository
1. Sign in to GitHub and navigate to the [**chirpy-starter**](https://github.com/cotes2020/chirpy-starter).
2. Click the <kbd>Use this template</kbd> button and then select <kbd>Create a new repository</kbd>.
3. Name the new repository `<username>.github.io`, replacing `username` with your lowercase GitHub username.


## Configuration

Update the variables in `_config.yml`{: .filepath} as needed. Some typical options include:

* `title` (line 17)
* `tagline` (line 19)
* `url` (line 26)
* `username` (line 29)
* `avatar` (line 101)
* `timezone` (line 12)
* `lang` (line 9)
* `email` (line 38)

When you are done click <kbd>Commit changes...</kbd>.

## Setting your avatar
To set your avatar which is the profile picture at the top of your navigation bar simply choose a folder such as `assets`{: .filepath} and upload a square image into it and click <kbd>Commit changes...</kbd>. After that go to `_config.yml`{: .filepath} line 101 and type `/YourFolderName/ImageName.ImageType`{: .filepath}. For example `/assets/Lojayn.png`{: .filepath}. Then click <kbd>Commit changes...</kbd>.

## About tab
To edit the the About tab on your website go to the `_tabs`{: .filepath} folder and go into `about.md`{: .filepath}. Proceed to removing the following lines.
``` Markdown
> Add Markdown syntax content to file `_tabs/about.md`{: .filepath } and it will show up on this page.
{: .prompt-tip }
```
After removing thoes lines you can write whatever you want using [**Markdown syntax**](https://www.markdownguide.org/basic-syntax/)

## Making a post
I will be going over the essentials of making a post if you would like a more detailed explanation visit the official web page by the creator of this template at <https://chirpy.cotes.page/posts/write-a-new-post/>

### Create a new file
To make a new post you must navigate to the `_posts`{: .filepath} folder in your repository. From there click <kbd>Add file</kbd> and then click <kbd>Create new file</kbd> from the dropdown menu. Make sure to name the file in the following format `YYYY-MM-DD-Insert-Title-Here.md`{: .filepath}

### Front Matter
Basically, you need to fill the Front Matter as below at the top of the post:

```yaml
---
title: TITLE
date: YYYY-MM-DD HH:MM:SS +/-TTTT
categories: [TOP_CATEGORIE, SUB_CATEGORIE]
tags: [TAG]     # TAG names should always be lowercase
---
```
### Writing a post
After you have created the front matter you can write whatevery you want using [**Markdown syntax**](https://www.markdownguide.org/basic-syntax/)

### Getting an image on your post
To put a video in your post choose a folder such as `assets`{: .filepath} and upload a image into it and click <kbd>Commit changes...</kbd>. After that go to your post and type the following.
```Markdown
![img-description](/path/to/image)
_Image Caption_
```
Replace `/path/to/image` with the path to your image. For example `/assets/Lojayn.png`{: .filepath}. Then click <kbd>Commit changes...</kbd>.

### Getting a video on your post
To put a video in your post choose a folder such as `assets`{: .filepath} and upload a video into it and click <kbd>Commit changes...</kbd>. After that go to your post and type
```liquid
{% raw %}
{% include embed/video.html src='{URL}' %}
{% endraw %}
```
. Replace URL with the path to your video. For example `/assets/Lojayn.mp4`{: .filepath}. Then click <kbd>Commit changes...</kbd>.

## Video
This is the video I used while making this website however I did not use jekyll or VS code i only used it to learn how to start up my website.

{% include embed/youtube.html id='fX8d3SgdTbo' %}
