---
layout: intellij
title: idea解除svn关联
date: 2019-09-09 23:04:33
categories:
- Tool
tags:
- SVN
- IDEA
keywors: 
description: 
top_img: 
cover: /img/svn.jpg
---

有时候项目需要解除svn关联，百度谷歌几篇，以下方式是最简单快捷的。

从.idea文件夹下手，找到了cvs.xml，其内容如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project version="4">
  <component name="VcsDirectoryMappings">
    <mapping directory="" vcs="svn" />
  </component>
</project>
```

1. <component> </component>节点及其内容全部注释掉
2. 直接删除掉.idea文件