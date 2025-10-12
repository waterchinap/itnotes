---
title: 插入frontmatter
lastUpdated: 2025-10-11
---
starlight要求每个md文章都必须要有frontmatter, 并且要求必须有title这个属性，为了避免每次都重复输入这些内容，研究了实现路径。
## 核心模板

无法实现自动插入文件名，需要手动输入。

## 社区templater

可以实现，思路如下：

- 设置好模板，主要就是加上frontmatter和两个参数。并且为这个模板设置快捷为`alt+a`.
- 新建文件，在头部输入一号标题，obsidian会自动将其做为文件名。
- 使用快捷键，实现加入frontmatter的功能。