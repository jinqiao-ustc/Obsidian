---
title: Admotion
date created: 2022-10-07 16:55:41
date modified: 2022-10-08 22:20:28
---
## Admotion

- 为文档添加小卡片
- 代码块加 ad-…变为小卡片，卡片其他参数
	- title：标题
	- collapse：折叠情况
	- icon：图标
	- color：颜色
- icon 与 color 可以通过 ad- 后面的 type 来指定默认值
- 块内渲染代码块用 `~~~` 代替原来的 ```
- 也可以创建行内格式（适配不太好，尽量少用）
	```text
	!!! ad-类型 标题

	内容

	--- admonition
	```
	```ad-example
	这是个例子
	```
- 跟 tag 很像，但是优势在于美观，平时自己写东西自己看的话可以用 tag，写报告一类的可以用这个
