---
layout: post
title: 解决jekyll(github page)无法正确解析nested lists的问题
category: blog
---

##问题描述

由于Jekyll默认使用maruku来解析markdown，而maruku无法正确解析markdown中的nested lists。示例如下：
    
    * I'm the first layer
        * I'm the second layer
        * Me too
    * I'm the first layer, item 2
    * I'm item 3

正常情况下，这段文字应该显示为：

* I'm the first layer
    * I'm the second layer
    * Me too
* I'm the first layer, item 2
* I'm item 3

但由于maruku的bug，会变成：

* I'm the first layer * I'm the second layer * Me too
* I'm the first layer, item 2
* I'm item 3

其实除了这个问题之外，maruku有时还无法正确解析「块引语」。现在可以一并解决。

##解决方案
通过google找到了问题的[解决方案](https://github.com/mojombo/jekyll/issues/190)。解决方案非常简单。因为这不是jekyll的问题，因此只需要更换markdown parser。Mac用户安装rdiscount
    
    sudo gem install rdiscount

然后在站点的`_config.yml`文件中最后加入

    markdown: rdiscount

即可。如果不需要在本地使用jekyll，那么可以省略第一步，直接修改`_config`然后push。

需要注意的是，将修改push到github后，可能需要一段时间才会有页面更新。如果push之后github page半天没有反应，只要本地页面显示正常，那就不要急，耐心等待即可。
