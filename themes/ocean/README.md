# Ocean

Ocean is a mobile-enabled Hexo theme based on the features in Hexo's default theme landscape. Since I am a Designer and not a Coder, so please advise! I am very grateful to [youchen1992](https://github.com/youchen1992) for providing technical support during the Ocean production process.


[Preview](https://zhwangart.github.io)

[中文说明](https://zhwangart.github.io/2018/11/30/Ocean/)

![Screenshot](screenshots/hexo-theme-ocean.jpg)

### Install

``` bash
$ git clone https://github.com/zhwangart/hexo-theme-ocean.git themes/ocean
```

### Enable

Modify `theme` setting in `_config.yml` to `ocean`

``` yml
theme: ocean
```

### Update

``` bash
cd themes/ocean
git pull
```

### Configuration

let me know if you can’t find something.

``` yml
# Menu
menu:
  Home: /
  Archives: /archives
  Gallery: /gallery
  About: /about
rss: /atom.xml

# Miscellaneous
favicon: /favicon.ico
brand: /images/hexo.svg

# Ocean Video
# Because I put videos in multiple formats on the same path, I just labeled the path here.
ocean:
  overlay: true
  path: images/ocean/      # Video storage path, formats: mp4/ogg/webm
  brand: /images/hexo-inverted.svg      # Optional, a small logo

# Content
excerpt_link: Read More...
share_text: Share
nav_text:
  page_prev: Prev page
  page_next: Next page
  post_prev: Newer posts
  post_next: Olde posts

# fancybox
fancybox: true

# Local search
search_text: Search

# Gitalk
gitalk:
  enable: true
  clientID: # GitHub Application Client ID
  clientSecret: # Client Secret
  repo: # Repository name
  owner: GitHub ID
  admin: GitHub ID
```

The [feathericon](https://feathericon.com) in the menu is programmed ordely in "CSS `source/css/_partial/navbar.styl` " and can be changed or added if needed.

``` css
.nav-item
  &:nth-child(1)         // home
    .nav-item-link
      &::before
        content '\f12f'
  &:nth-child(2)         // archives
    .nav-item-link
      &::before
        content '\f12a'
  //&:nth-child(3)         // gallery
  //  .nav-item-link
  //    &::before
  //      content '\f1a9'
  //&:nth-child(4)         // about
  //  .nav-item-link
  //    &::before
  //      content '\f174'
```

### Plugins

+ [hexo-generator-search](https://github.com/hexojs/hexo-theme-landscape) Local search
	
  ```yml
  $ npm install hexo-generator-searchdb --save
  ```
  Then add the plugin configuration for hexo's configuration file `_config.yml` (note: not the theme's configuration file):
  
  ```yml
  # Hexo-generator-search
  search:
    path: search.xml
    field: post
    format: html
  ```

+ [hexo-generate-feed](https://github.com/hexojs/hexo-generator-feed) RSS

  ```yml
  $ npm install hexo-generator-feed --save
  ```
  
  Then add the plugin configuration for hexo's configuration file `_config.yml` (note: not the theme's configuration file):
  
  ```yml
  feed:
      type: atom
      path: atom.xml
      limit: 20
      hub:
      content:
      content_limit: 140
      content_limit_delim: ' '
      order_by: -date	
  ```
  
+ [hexo-generator-index-pin-top](https://github.com/netcan/hexo-generator-index-pin-top)
	
	``` bash
  $ npm uninstall hexo-generator-index --save
  $ npm install hexo-generator-index-pin-top --save
  ```

### Post poster

``` md
---
title: Post name

photos: [
        ["img_url"],
        ["img_url"]
        ]
---
```

### Gallery
Need to write in the head of the markdown, this is not a good way to write, I hope to get a better way to write on github.

``` md
---
title: Gallery

albums: [
        ["img_url","img_caption"],
        ["img_url","img_caption"]
        ]
---
```

### Toc

Use Tocbot to parse the title tags (h1~h6) in the content and insert the directory. 

+ ocean/_config.yml

	``` bash
	# Toc
  toc: true
	```
+ If Toc is turned on in ocean/_config.yml, then Tocbot will generate a Toc article directory in the title tag of each blog parsing content, but not all blogs require Toc, so in the Front-matter section of markdown Can be closed:

	``` md
	---
  toc: false
  ---
	```

---

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br><span xmlns:dct="http://purl.org/dc/terms/" property="dct:title">Ocean</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="https://github.com/zhwangart/hexo-theme-ocean" property="cc:attributionName" rel="cc:attributionURL">zhwangart</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License</a>.
