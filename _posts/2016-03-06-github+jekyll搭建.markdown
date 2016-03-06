---
layout: post
title:  "github+jekyll博客搭建实践"
date:   2016-03-06 16:35:00
categories: jekyll 
---

windows下安装：

首先安装python,ruby,Pygments

ruby 安装包：

注：ruby不能安装在带有空格的路径里，否则后面运行jekyll时会报错：

C:\Users\Administrator>jekyll
'""E:\Program' 不是内部或外部命令，也不是可运行的程序
或批处理文件。

Pygments（语法高亮插件 ）

C:\Users\Administrator>easy_install Pygments

安装jekyll:

C:\Users\Administrator>gem install jekyll

出错如下：
ERROR:  While executing gem ... (Gem::RemoteFetcher::FetchError)
    Errno::ECONNABORTED: An established connection was aborted by the software i
    n your host machine. - SSL_connect (https://api.rubygems.org/quick/Marshal.4.8/c
    olorator-0.1.gemspec.rz)



    gem源有问题：

    查看gem源：

    C:\Users\Administrator>gem source
    *** CURRENT SOURCES ***

    https://rubygems.org/


    删除gem源：

    C:\Users\Administrator>gem sources --remove https://rubygems.org/
    https://rubygems.org/ removed from sources

    C:\Users\Administrator>gem source
    *** CURRENT SOURCES ***


    添加gem源：（注：现在taobao的好像不能用了）

    C:\Users\Administrator>gem sources -a http://ruby.taobao.org/
    Error fetching http://ruby.taobao.org/:
            bad response Not Found 404 (http://ruby.taobao.org/specs.4.8.gz)

            解答：
            我们已经停止基于 HTTP 协议的镜像服务, 请在配置中使用 HTTPS* 协议代替

            Q: gem install xxx 的时候遇到错误信息包含：
            “Error fetching data: Errno::ETIMEDOUT: Operation timed out - connect(2)”

            A: 网络问题导致请求淘宝服务器被连接重置了，在遇到此类情况的时候，你可以尝试换一台机器或网络尝试安装，
            看是否还有同样的问题，以确定是淘宝镜像服务器的问题还是你的环境问题，如果你换了环境仍然有问题，
            请上 Ruby China 发帖求助。

            将 http://ruby.taobao.org/  改为　https://ruby.taobao.org/ 即可!


            C:\Users\Administrator>gem sources -a https://ruby.taobao.org/
            https://ruby.taobao.org/ added to sources

            更新gem缓存：

            C:\Users\Administrator>gem source -u
            source cache successfully updated


            C:\Users\Administrator>gem install jekyll
            Fetching: ffi-1.9.10-x86-mingw32.gem (100%)
            Successfully installed ffi-1.9.10-x86-mingw32
            Fetching: rb-inotify-0.9.7.gem (100%)
            Successfully installed rb-inotify-0.9.7
            Fetching: rb-fsevent-0.9.7.gem (100%)
            Successfully installed rb-fsevent-0.9.7
            Fetching: listen-3.0.6.gem (100%)
            ……

            进入已前已建好的项目目录：


G:\study\space\site\site>jekyll server
   Configuration file: G:/study/space/site/site/_config.yml
   Deprecation: You appear to have pagination turned on, but you haven't inc
   luded the `jekyll-paginate` gem. Ensure you have `gems: [jekyll-paginate]` in yo
   ur configuration file.
   Source: G:/study/space/site/site
   Destination: G:/study/space/site/site/_site
   Incremental build: disabled. Enable with --incremental
   Generating...
   Build Warning: Layout 'none' requested in feed.xml does not exist.
   done in 0.536 seconds.
   Deprecation: You appear to have pagination turned on, but you haven't inc
   luded the `jekyll-paginate` gem. Ensure you have `gems: [jekyll-paginate]` in yo
   ur configuration file.
   Please add the following to your Gemfile to avoid polling for changes:
   gem 'wdm', '>= 0.1.0' if Gem.win_platform?
   Auto-regeneration: enabled for 'G:/study/space/site/site'
   Configuration file: G:/study/space/site/site/_config.yml
   Server address: http://127.0.0.1:4000/site/
   Server running... press ctrl-c to stop.
   [2016-03-03 16:20:50] ERROR `/favicon.ico' not found.
   [2016-03-03 16:20:50] ERROR `/favicon.ico' not found.

   如新建项目，则：

    G:\study\space\site\newtest>jekyll new newtest

    jekyll主页：http://jekyll.bootcss.com/
    安装出错参考：http://blog.csdn.net/kong5090041/article/details/38408211



