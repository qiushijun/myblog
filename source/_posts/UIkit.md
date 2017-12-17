---
title: UIkit
tags: 
	- Web前端
---

UIkit 是 YOOtheme 团队开发的一款轻量级、模块化的前端框架，可快速构建强大的web前端界面。UIKit提供了全面的HTML、CSS及JS组件，它们使用简单，容易定制和扩展。
它基于LESS开发，代码结构清晰简单，易于扩展和维护，并且具有体积小、反应灵敏的响应式组件，你可以根据 UIKit 基本的风格样式，轻松地自定义创建出自己喜欢的主题样式。
<!-- more -->
## HTML页面代码

首先，确保你有一个可靠的代码编辑器，比如 Sublime Text 2。你需要在你的HTML5的文档头部添加编译后的UIKit CSS和JavaScript文件，最好是压缩后的版本。别忘了添加 jQuery。就是这样！
##### Example
{% codeblock %}
<!DOCTYPE html>
<html>
    <head>
        <title></title>
        <link rel="stylesheet" href="uikit.min.css" />
        <script src="jquery.js"></script>
        <script src="uikit.min.js"></script>
    </head>
    <body>
    </body>
</html>
{% endcodeblock %}