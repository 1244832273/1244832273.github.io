---
title: hexo多台电脑使用#文章页面上的显示名称，一般是中文
date: 2020-7-21 #文章生成时间，一般不改，当然也可以任意修改
categories: 奇淫技巧 #分类
tags: [hexo, 奇淫技巧] #文章标签，可空，多标签请用格式，注意:后面有个空格
description: 来自 https://juejin.im/post/5acf22e6f265da23994eeac9   还有  https://www.zhihu.com/question/21193762
---

#### 一、关于搭建的流程

\1. 创建仓库，[http://CrazyMilk.github.io](https://link.zhihu.com/?target=http%3A//CrazyMilk.github.io)；
\2. 创建两个分支：master 与 hexo；
\3. 设置hexo为默认分支（因为我们只需要手动管理这个分支上的Hexo网站文件）；
\4. 使用git clone git@github.com:CrazyMilk/CrazyMilk.github.io.git拷贝仓库；
\5. 在本地[http://CrazyMilk.github.io](https://link.zhihu.com/?target=http%3A//CrazyMilk.github.io)文件夹下通过Git bash依次执行npm install hexo、hexo init、npm install 和 npm install hexo-deployer-git（此时当前分支应显示为hexo）;
\6. 修改_config.yml中的deploy参数，分支应为master；
\7. 依次执行git add .、git commit -m "..."、git push origin hexo提交网站相关的文件；
\8. 执行hexo g -d生成网站并部署到GitHub上。

#### 二、关于日常的改动流程

\1. 依次执行git add .、git commit -m "..."、git push origin hexo指令将改动推送到GitHub（此时当前分支应为hexo）；
\2. 然后才执行hexo g -d发布网站到master分支上。

#### 三、本地资料丢失后的流程

\1. 使用git clone git@github.com:CrazyMilk/CrazyMilk.github.io.git拷贝仓库（默认分支为hexo）；
\2. 在本地新拷贝的[http://CrazyMilk.github.io](https://link.zhihu.com/?target=http%3A//CrazyMilk.github.io)文件夹下通过Git bash依次执行下列指令：npm install hexo、npm install、npm install hexo-deployer-git（记得，不需要hexo init这条指令）。