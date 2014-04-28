---
layout: post
title: "Tips on Customizing Octopress"
date: 2014-04-27 14:55:38 -0400
comments: true
categories: "Octopress Octopress&nbspThemes Font&nbspAwesome Octopress&nbspLayouts"
---

### So you're lost on setting up an octopress blog.
<hr>
Having had just made my own Octopress Blog, I can safely say there are some things I wish were clearer about setting one up. So that's what this blog post is about! This post <i>isn't</i> about how to make a Octopress blog, for that you should go to http://octopress.org/docs/setup/ and follow the other tutorials to get the basics going. Rather this is about some pointers on how Octopress works and how to customize some things.

### Getting a better theme.
<hr>
While Octopress is great it ain't too pretty.  After running ```rake setup_github_pages```, which sets up your url and the correct branch for your repo, is a good time to get a new theme.  [This](https://github.com/imathis/octopress/wiki/3rd-Party-Octopress-Themes) will help you find an awesome theme built specifically for Octopress all come with site previews as well as the necessary steps to using their theme. Here are the generic steps for your command line:
```
$ cd octopress
$ git clone GIT_URL .themes/THEME_NAME
$ rake install['THEME_NAME']
$ rake generate
```

### Customizing your style sheets.
<hr>
Octopress can be a little confusing since it has many duplicate files and directories.  But all the changes to you'll need to make are going to be in ```sass/``` directory on the top level of Octopress.  In that directory will be a ```custom/``` dir with will be ```_styles.scss```.  That file is where all your complex custom css or sass will go.  If you want to change say, a default color your theme uses change that in the ```_color.css``` in the same ```custom/``` directory.

The way Octopress works is that it will require ```sass/screen.scss```, which in turn requires partials on the top level of the ```sass/```, which those partials compile the styles of each other directory in ```sass/```.  The style sheets in ```custom/``` are compiled last and ```custom/_styles.scss``` is the last of last, letting its styling override all others.

For more about customizing your style sheets see http://octopress.org/docs/theme/styles/

### Getting Font Awesome
<hr>
Skip this if Font Awesome works with your theme.

Font Awesome lives up to its name, but getting it into your Octopress blog can be a pain, but luckily I've found a great [blog](http://infectiouscoding.com/blog/2013/09/04/add-font-awesome-to-octopress/) that helped. Here's the gist:<br>
1) Download and unzip [Font Awesome](http://fortawesome.github.io/Font-Awesome/).<br>
2) If you don't have a ```sass/font-awesome/``` folder then create one and move the ```scss/``` from the Font Awesome package you just downloaded into ```sass/font-awesome/```. This will create a dir tree that looks like ```sass/font-awesome/scss/```.<br>
3) Finish up in ```sass/font-awesome/scss/``` by adding this line, ```@import "font-awesome/scss/font-awesome.scss"```, to ```screen.scss``` in the top level of ```sass/```.<br>
4) Next move the ```font/``` from the Font Awesome package to ```source/font/``` if it isn't there create it, if it is with Font Awesome files in it delete those files and replace them with the new ones.  You'll end up with a dir tree that looks like ```source/font/```.<br>

Font Awesome should be good to go now.

### Setting Up Your Layouts
<hr>
For almost anything else you'd want to do with Octopress you'll be in ```source/```, which is the top directory of your Octopress blog.  When you run ```rake new_post["title"]``` it will create your markdown file here in ```source/_posts/```.  With Octopress your pages use something like what's shown below to create their complete layout.  It's kind of like in Rails when a block of html yields to a partial to render another part of a page.
```
  ---
  layout: default
  title: Blog Archives
  ---
```
In ```_layouts/``` you'll be able to make your own if you want. These layouts often require partials from ```_inlcudes/```.  Once you finish making your own layout add ```layout: some_custom_layout``` to your page.

For more of what you can do in ```source/``` check out http://octopress.org/docs/theme/template/

### Getting a New Favicon
<hr>
Getting a new favicon for your blog isn't too hard; there are just several favicon.pngs floating around the octopress blog.  But the real favicon, or at least the one which is actually used by octopress is in the top level of ```source/```.  I simply deleted this favicon and replaced it with my own.  Next to make sure it is properly being required by the head of your blog's page.  In ```source/_includes/head.html``` is the line:
``` html
<link href="{{ root_url }}/favicon.ico" rel="shortcut icon">
```
Make sure the ```favicon.ico``` part is the exact same name as your favicon on the top level of ```source/``` and you should be good to go. 

\- If you change the favicon and don't see it, try clearing your browser's cache in order to properly update the blog's styling.