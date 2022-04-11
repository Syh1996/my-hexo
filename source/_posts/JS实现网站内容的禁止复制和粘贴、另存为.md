---
title: JS实现网站内容的禁止复制和粘贴、另存为
date: 2021-02-21 12:52:04
tags: javascript
---

``````html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>菜鸟教程(runoob.com)</title>
</head>
<body>

<p id="p"><strong>注意：</strong> 该实例如果在 Chrome, Firefox 和 Opera 浏览器中可能无法工作。</p>
<script>
function myFunction(e) {
	console.log(e);
  return false;
}
	document.querySelector("#p").oncopy=myFunction;
</script>

</body>
</html>
``````

