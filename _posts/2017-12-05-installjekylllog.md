---
layout: post
title: "安装jekyll打开别人的项目出现的问题"
date: 2017-12-04 15:19:06 
description: "安装jekyll打开别人的项目出现的问题"
tag: 其他
---
<h3>一、前提</h3>
1. WIN10 安装好  ruby,devkit,git
2. 在CMD中使用ruby -v;gem -v,查看是否安装正确
3. 使用gem install jekyll,用jekyll -v查看是否安装正确。
<p> </p>
<h3>二、测试</h3>
1. 在CMD下使用命令：jekyll new myBlog,cd myBlog,jekyll serve
2. 在浏览器里输入： http://localhost:4000.
3. 若能打开jekyll网页说明环境没问题了。

<h3>三、下载别人的项目</h3>
1. 如使用git下载链接https://github.com/onevcat/vno-jekyll.git到本地
2. 在下载的目录SHIFT+鼠标右键，打开powershell
3. 输入jekyll serve,报错：Could not find public_suffix-3.0.0 in any of the sources (Bundler::GemNotFound)
4. 再输入jekyll -v发现找不到jekyll了。

<h3>四、解决办法</h3>
1. 安装ruby的devkit,链接https://rubyinstaller.org/downloads/，在下面的“DEVELOPMENT KIT”
2. 安装python2.x版本
3. 添加各种path环境变量C:\ruby\bin;C:\devkit\bin;C:\git\bin;C:\Python\App;C:\devkit\mingw\bin
4. 在该项目下powershell（CMD）中，gem updtae,bundle install, bundle update,
5. jekyll serve命令OK了。

<h3>五丶git命令</h3>
1. 克隆github项目 git clone [源地址]
2. 提交顺序 git add .   git commit -a -m "v*.*.* commit"   git push origin master


<h3>六、其他BUG</h3>
1. markdown文件命名格式一定要正确，例：2017-10-22-KMP.md，前面是年月日-名字，名字最好不要用中文。
2. markdown文件中前面的头部格式也是一成不变，尤其日期不能出错。
3. 很诡异的事情，2017.12.05 15:15，在md文件抬头中的date:写2017-12-05不会显示，改成2017-12-04就显示了，WTF？


