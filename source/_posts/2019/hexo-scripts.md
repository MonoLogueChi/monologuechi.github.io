---
title: Hexo脚本
date: 2019-11-29 19:48:35
tags: ["建站笔记"]

keywords: Hexo scripts 脚本
comments: true
---

事情的起因是这样的，我博客里用的音乐插件[hexo-tag-aplayer](https://github.com/MoePlayer/hexo-tag-aplayer)太久没更新了，已经不能支持新版的[MetingJS](https://github.com/metowolf/MetingJS)，我便决定自己尝试解决一下。

<!-- more -->

{% mmedia "meting" "id:002vALgR3hRRlv" "server:tencent" "type:artist" %}

对于 js 那一套东西，我实在是不懂，所以开发一个插件就算了，打算写一个简单一点的脚本就好。所有 hexo 插件实现的功能，都可以用脚本实现，功能太复杂了，而且想要把这个东西共享给别人，就会打包成插件去发布。

关于 Hexo 脚本和插件的介绍，可以看[官方文档](https://hexo.io/zh-cn/docs/plugins#%E8%84%9A%E6%9C%AC%EF%BC%88Scripts%EF%BC%89)。

脚本开发我也是个半吊子，只会写一些简单的脚本。

我写的 Aplayer 脚本的开源地址：[hexo-tag-aplayer-scripts](https://github.com/MonoLogueChi/hexo-tag-aplayer-scripts)。

已合并到新项目：[hexo-tag-mmedia](https://github.com/u2sb/hexo-tag-mmedia)

## 目的

简单介绍一下怎么写脚本，就拿上面这个脚本为例。我的目的是，在文章中插入标签：

```
{% meting "60198" "netease" "playlist" %}
```

在网页中要自动生成代码：

```js
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/aplayer/dist/APlayer.min.css">
<script src="https://cdn.jsdelivr.net/npm/aplayer/dist/APlayer.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/meting@2/dist/Meting.min.js"></script>
<meting-js
	server="netease"
	type="playlist"
	id="60198">
</meting-js>
```

## 基础版

看过[文档](https://hexo.io/zh-cn/api/tag)以后，大致知道怎么写了，首先是需要一个标签：

```js
hexo.extend.tag.register("meting", function (args) {
  return "返回需要插入网页的内容";
});
```

这样，在文章中写`{% meting %}`标签就不会报错了。接下来就是需要处理里面的内容了，因为对 js 和 hexo 不是很熟，就先从最简单的开始，跑通了以后再优化。注意一点，参数`args`是一个数组，传入的每一个参数都说数组里面的一个元素，比如

```
{% meting "60198" "netease" "playlist" %}
```

取参数的时候分别是`args[0]`、`args[1]`、`args[2]`。

```js
hexo.extend.tag.register("meting", function (args) {
  return `
        <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/aplayer/dist/APlayer.min.css">
        <script src="https://cdn.jsdelivr.net/npm/aplayer/dist/APlayer.min.js"></script>
        <script src="https://cdn.jsdelivr.net/npm/meting@2/dist/Meting.min.js"></script>
        <meting-js
	        server="${args[1]}"
	        type="${args[2]}"
	        id="${args[0]}">
        </meting-js>`;
});
```

这样，一个最简单脚本就写出来了，可以接收三个参数，自动插入 meting 标签。

## 稍进阶

上面的脚本是能跑了，但是十分简陋，支持程度也不高，要进一步加强。首先是要对脚本能配置，查 api 以后可以知道，获取配置需要用到`hexo.config`，所以：

```js
const config = hexo.config.aplayer;
```

这样就可以获取到`_config.yml`中`aplayer`项的配置了。除了这三个标签，其他标签怎么搞？还需要一个通用的解析方法：

```js
function GetOptions(args) {
  var options = new Array();
  args.forEach((option) => {
    var d = option.indexOf(":");
    if (d != -1) {
      var key = option.substring(0, d);
      var value = option.substring(d + 1, option.length);
      options[key] = value;
    }
  });
  return options;
}
```

这个方法可以将`key:value`解析成一个字典（我不知道 js 里怎么称呼，我就用 C#里的称呼，称他为字典吧）。后面这一步跨步有点大，而且我写的代码又不规范，将就着看一下吧：

```js
hexo.extend.tag.register("meting", function (args) {
  try {
    var tag = CreatMetingTag(args);
    return tag;
  } catch (e) {
    console.error(e);
    return `
		<script>
			console.error("${e}");
		</script>`;
  }
});

function CreatMetingTag(args) {
  var jsCdn =
    config.cdn != null && config.cdn.length > 0
      ? config.cdn
      : "https://cdn.jsdelivr.net/npm/aplayer/dist/APlayer.min.js";
  var cssCdn =
    config.style_cdn != null && config.style_cdn.length > 0
      ? config.style_cdn
      : "https://cdn.jsdelivr.net/npm/aplayer/dist/APlayer.min.css";
  var metingCdn =
    config.meting_cdn != null && config.meting_cdn.length > 0
      ? config.meting_cdn
      : "https://cdn.jsdelivr.net/npm/meting/dist/Meting.min.js";
  var text = `<link rel="stylesheet" href="${cssCdn}"><script src="${jsCdn}"></script><script src="${metingCdn}"></script>`;
  text =
    (config.meting_api != null && config.meting_api.length > 0
      ? `<script>var meting_api='${config.meting_api}?server=:server&type=:type&id=:id&auth=:auth&r=:r';</script>`
      : "") + text;
  var op = GetOptions(args);
  if (!("auto" in op) && !("id" in op)) {
    op["id"] = args[0];
    op["server"] = args[1];
    op["type"] = args[2];
  }
  var metingOptions = "";
  for (var key in op) {
    metingOptions += `${key}="${op[key]}"`;
  }
  text += `<meting-js ${metingOptions}></meting-js>`;
  return text;
}

function GetOptions(args) {
  var options = new Array();
  args.forEach((option) => {
    var d = option.indexOf(":");
    if (d != -1) {
      var key = option.substring(0, d);
      var value = option.substring(d + 1, option.length);
      options[key] = value;
    }
  });
  return options;
}
```

## 后续更新

先起一个头，更新的话后面慢慢再来了，现在我自己用是足够了。

最近半个月估计没啥时间了，后面有个开箱视频要做，还有一个 NAS 的教程和视频需要做。
