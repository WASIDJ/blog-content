---
author: Ryou
title: 更加方便的创建Obsidian模板
date: 2026-01-16 23:00:00
lastmod: 2026-06-10 10:00:00
description: Obsidian模板创建以及更加便捷的写作方式
categories:
  - Done
tags:
  - blog
  - hugo
  - obsidian
draft: false
image: https://cdn.jsdelivr.net/gh/wasidj/picBed@main/img/wechatQR.png
---

>[!task] 工作步骤
>1. [ ] 在OBSIDIAN中完成 formatter yaml 模板(添加 动态图片链接 更新时间)
>   - [x] 我的obsidian中的picgo插件为什么不自动生效？
>1. [x] `crontab` 完成 每周/天 固定时间段 `/home/ryou/scripts/auto_push_blog_content.sh`
>2. [ ] 支持更多的markdown语法 
>3. [ ] 添加`formatter` 字段 天气 坐标 该文档的 `git log`

## 前言
在使用`Obsidian`进行写作时，模板的使用可以大大提高写作效率。本文将介绍如何创建和使用`Obsidian`模板，以便更加方便地进行写作。

## formatter Yaml

| 字段          | 描述                            |
| ----------- | ----------------------------- |
| author      | 作者名称 默认 ryou                  |
| draft       | 是否公开，默认 true                  |
| image       | 文章封面图片链接 （需要随机返回wallhaven.cc） |
| title       | 文章标题                          |
| description | 文章描述                          |
| date        | 文章创建日期                        |
| lastmod     | 文章最后修改日期                      |
| categories  | 文章分类                          |
| tags        | 文章标签                          |

---
## 参考资料
- 