---
title: 配置markdown工具
date: 2025-10-13
---

不一定要使用obsidian来做为md的编辑工具了。codium更加简单。

## 快速插入snippet

这个很简单，使用内置的snippet config功能，在markdown的配置文件中添加如下代码：

```json
"add frontmatter": {
    "prefix": "/fm",
    "body": [
        "---",
        "title: ${TM_FILENAME_BASE}",
        "date: $CURRENT_YEAR-$CURRENT_MONTH-$CURRENT_DATE",
        "---"
    ],
    "description": "Add front matter with current filename and date"
}
```

## 复制粘贴图片

这个要复杂一点，我尽量使用的是内置的功能，可以实现的效果为：图片保存在assets目录下，并自动添加图片的引用。图片名变为：文件名加时间戳，并自动添加图片的引用。

主要配置是加了一条：

```json
    "markdown.copyFiles.destination": {
        "**/*.md": "assets/${documentBaseName}${unixTime}.${fileExtName}"
    }
```

![alt text](assets/配置markdown工具1760337014903.jpeg)