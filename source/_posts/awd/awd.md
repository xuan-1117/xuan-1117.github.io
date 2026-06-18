---
title: "awd"
date: 2026-03-21 22:58:07
categories: "awd"
tags:
  - "awd"
cover: /img/backgrounds/bg-01.png
top_img: /img/backgrounds/bg-01.png
disableNunjucks: true
---

1.备份www文件

2.上传waf.php,在网站跟目录下的index.php包含此文件

3.用d盾排查后门

4.审计站点源码

发现game目录，浏览器访问此目录，出现一个游戏页面

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/awd/awd/img-001.png)

点进去目录，也有index文件

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/awd/awd/img-002.png)

查看文件，分析源码

```plain
<?php
session_start();
error_reporting(0);
?>

<?php
if (isset($_GET['name'])) {
	$_SESSION['name'] = base64_encode($_GET['name']);
}

if (empty($_SESSION['name'])){
	$_SESSION['name']=base64_encode('me');
	}
?>

<!DOCTYPE html>
<html lang="zh-CN">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <link rel="stylesheet" href="css/style.css">
  <title>连连看</title>
</head>

<body>
    <div class="container">
        <div class="heading">
            <p class="time"></p>
            <?php
            if (!empty($_SESSION['name'])) {
	echo "<button id=\"help\" class=\"btn\" >help ".base64_decode($_SESSION['name']) . "</button>";
}
?>
            <button id="restart" class="btn" >restart</button>
            <a href="./?file=readme.txt">
            <button class="btn">about</button>
          </a>
        </div>
        <div class="grid-container"></div>
    </div>
    <script src="js/config.js"></script>
    <script src="js/util.js"></script>
    <script src="js/view.js"></script>
    <script src="js/game.js"></script>
    <script src="js/event.js"></script>
    <script src="js/main.js"></script>
 <?php if (isset($_GET['file'])) {
	include($_GET['file']);
}?>
</body>

</html>
```

审计代码存在两个漏洞，以下是文件包含漏洞，利用漏洞读取flag文件浏览器访问：ip/game/?file=/flag

```plain
 <?php if (isset($_GET['file'])) {
	include($_GET['file']);
}?>
```

可以读出flag,删除此代码，包含上waf.php

接着扫描ip,读取其他靶机的flag

