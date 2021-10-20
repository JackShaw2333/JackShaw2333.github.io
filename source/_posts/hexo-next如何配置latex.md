---
title: hexo next如何配置latex
date: 2020-09-13 19:52:53
tags: 
- hexo
---

# Introduction

本文总结了如何在next主题中成功配置latex。

1年前曾经试图解决过这个问题，模仿了不少博客的帖子都没有成功。奈何本人前端知识储备几乎为0，因此总是无法解决遇到的问题。

恰逢学期伊始，出于课程内容总结需要，我再一次配置了latex，成功了！

参考了[hexo next主题解决无法显示Latex数学公式------系列第六篇](https://dragon-liu.github.io/2019/04/26/blog6/)，欣喜之余，不胜感激。


由于本人相关知识储备极少，本文只记录配置过程，相关知识不予记录，望读者谅解。
<!--more-->

我使用的hexo相关软件信息

```bash
$ git --version
git version 2.22.0.windows.1

$ hexo -v
hexo: 4.0.0
hexo-cli: 2.0.0
http_parser: 2.8.0
node: 10.16.1
v8: 6.8.275.32-node.54
uv: 1.28.0
zlib: 1.2.11
brotli: 1.0.7
ares: 1.15.0
modules: 64
nghttp2: 1.34.0
napi: 4
openssl: 1.1.1c
icu: 64.2
unicode: 12.1
cldr: 35.1
tz: 2019a

$ npm -v
6.13.1
```


# 更换hexo渲染引擎

将hexo默认的渲染引擎`hexo-renderer-marked`更改为`hexo-renderer-kramed`。

```bash
npm uninstall hexo-renderer-marked --save
npm install hexo-renderer-kramed --save
```

# 修改语义冲突

来到博客根目录下，更改`博客根目录/node_modules/kramed/lib/rules/inline.js`的相关内容。

## 修改escape变量

修改该文件下escape变量的值（11行）。

注释掉原先的旧值，改为新值。

```javascript
//escape: /^\\([\\`*{}\[\]()#$+\-.!_>])/,   旧值
escape: /^\\([`*\[\]()#$+\-.!_>])/,
```

## 修改em变量

修改该文件下escape变量的值（20行）。

```javascript
//em: /^\b_((?:__|[\s\S])+?)_\b|^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
em: /^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
```

# 在next主题中开启mathjax开关

进入到主题目录，我的是在`博客根目录/themes/next/`，修改此目录中的`_config.yml`文件。

修改后的大致结果如下所示：

```
# Math Formulas Render Support
math:
  enable: true

  # Default (true) will load mathjax / katex script on demand.
  # That is it only render those page which has `mathjax: true` in Front-matter.
  # If you set it to false, it will load mathjax / katex srcipt EVERY PAGE.
  per_page: true

  engine: mathjax
  
  # hexo-renderer-pandoc (or hexo-renderer-kramed) required for full MathJax support.
  mathjax:
    enable: true
    # See: https://mhchem.github.io/MathJax-mhchem/
    mhchem: true
```

# 在文章中打开mathjax开关

在需要latex渲染的文章开头的front-matter里打开mathjax开关。

```markdown
---
title: 
date: 
tags:
mathjax: true
---
```