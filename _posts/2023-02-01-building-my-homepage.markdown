---
layout: post
title:  "Building this page"
date:   2023-02-01 13:20:26 -0800
categories: github pages
---


Everyone that has a github account can host a static webpage by creating a one and only repo `YOUR_USERNAME.github.io` and github will magically host the page for you at `YOUR_USERNAME.github.io`.

(The service is called [**github pages**](https://pages.github.com/), go to the homepage for more details.)
(But basically you're only allowed to host static webpages. I guess...?) 

I wanted a homepage that does not look like pure HTML, and I found out there is a thing called [**jekyll**](https://jekyllrb.com/) which claims to be powering the github pages service.

So here it is, I'm using the [jekyll-theme-console](https://github.com/b2a3e8/jekyll-theme-console) theme, but there are also [free themes on github](https://github.com/topics/jekyll-theme) and some [cool but paid ones](https://jekyllthemes.io). Visit [here](https://jekyllrb.com/resources/) for more resources available.

The overall installation of the system is quite simple, but the files are quite large. After the system is installed, just fork a theme of your choice then clone and modify the files then you're good to go.

The rest is pretty much update the pages you want. The cool thing about this system is that all pages are generated from markdown files and are dynamic (but you'll need to push it onto github so maybe not so dynamic in this case). The setup of the home page is done through modifying *_config.yml* file, which is a YAML format file. Make sure the file is correctly indented otherwise you will get an error about malformed YAML, [something like this](https://stackoverflow.com/questions/33066015/jekyll-config-yml-did-not-find-expected-key-while-parsing-a-block-mapping)

I guess this is pretty much it. I'll see you in the next one :D