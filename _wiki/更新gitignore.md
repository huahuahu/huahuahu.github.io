---
layout: wiki  
title: 更新gitignore   
categories: tools    
keywords: git
---  


所有类型工程的 gitignore文件已经有模板了，在[这个网址](https://github.com/github/gitignore)。  
可以先下载模板，然后执行下面的命令

    git rm -r --cached .
    git add .
    git commit -m 'update .gitignore'  

第一句话把本地所有文件都改为 `untracked`状态，第二句话把本地所有文件都给为`tracked`状态，当然应用了新的过滤规则。    
在文档中，也有下面这句

> The purpose of gitignore files is to ensure that certain files not tracked by Git remain untracked.

>To stop tracking a file that is currently tracked, use git rm --cached.

1. [gitignore文档](https://git-scm.com/docs/gitignore)
2. [Git忽略规则及.gitignore规则不生效的解决办法](http://www.pfeng.org/archives/840)
3. [github/gitignore](https://github.com/github/gitignore)

