---
title: Ubuntu 安装必备
date: 2016-11-09 23:26:49
tags:
toc:
---

### Ubuntu初始化

#### 1. 安装无线驱动和更新系统以及语言

#### 2. 安装新立得和gdebi
`sudo apt-get install synaptic`
#### 3.安装软件

`sudo apt-get install git vim vim-gnome vim-gtk uget ariac`
>> 这里用synaptic安装特别方便还不容易出事故

<!--more-->
### 初始化github

#### 1. 安装ssh key

##### (1) 生成ssh key
`ssh-keygen -t rsa -C "your_email@youremail.com"`


##### （2）添加新的Key到ssh-agent

`eval "$(ssh-agent -s)"`
`ssh-add ~/.ssh/id_rsa`

##### (3) 添加到github

#### 2. 配置github信息
`git config --global user.name "Your Name"`
`git config --global user.email "your@email.com"`

#### 3. 同步远端代码
` git remote add origin git@github.com:zerolxf/zero.git `

### 翻墙
#### 1. shadowsocks安装
`sudo add-apt-repository ppa:hzwhuang/ss-qt5`
`sudo apt-get update`
`sudo apt-get install shadowsocks-qt5 `


#### 2. proxychains安装

`sudo apt-get install proxychains`

修改最后一行配置信息为socks5 127.0.0.1 1080
`sudo vim /etc/proxychains.conf`


#### 3. uget的安装：

`sudo add-apt-repository ppa:plushuang-tw/uget-stable`
`sudo apt-get update `
`sudo apt-get install uget`

#### 4. aria2的安装：

`sudo add-apt-repository ppa:t-tujikawa/ppa `
`sudo apt-get update `
`sudo apt-get install aria2`



=======
bcloud
chorme
shadowsock
星际翻译
git
nodejs & hexo
and so on
vim

### 参考链接

#### socks5 强转http
[privoxy](http://www.cnblogs.com/liuxuzzz/p/5324749.html)
#### 淘宝镜像安装hexo
[淘宝镜像](https://npm.taobao.org/)


> ~~nmp,nodejs直接用新立得安装~~ 
>这里还是到[nodejs官网](https://nodejs.org/en/),直接下载压缩包,然后解压,把解压后的bin目录添加到PATH环境变量中去,记得PATH=$PATH:xxx/bin
