# Firefox Extension Development

## 第一个扩展

### 编写

#### manifest.json

```json
{
    "manifest_version": 2,
    "name": "Borderify",
    "version": "1.0",
    "description": "Adds a red border to all webpages matching mozilla.org.",
    "icons": {
      	"48": "icons/border-48.png"
    },
    "content_scripts": [
      	{
        	"matches": ["*://*.mozilla.org/*"],
        	"js": ["borderify.js"]
      	}
    ],
    "browser_specific_settings": {			//optional. Add this if needed.
  		"gecko": {
    	"id": "borderify@example.com"
  		}
	}
}
```

`manifest_version` 在大部分情况下必须为 2，查看 [Browser compatibility](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/manifest_version#browser_compatibility) 获得更多信息。

#### icons

```json
"icons": {
  	"48": "icons/border-48.png",
  	"96": "icons/border-96.png"
}
```

必要的操作是提供 48\*48 以及 96\*96 的图标，以获得更高清的显示

#### borderify.js

创建一个 `borderify.js` 文件：

```js
document.body.style.border = "5px solid red";
```

### 测试扩展

#### 载入扩展

首先查看文件结构是否正确：

```
borderify/
	icons/
		border-48.png
		border-96.png
	borderify.js
	manifest.json
```

然后打开 Firefox，进入 <about:debugging> 链接 - 此 Firefox - 临时载入附加组件，然后选中 `borderify.js` 即可加载组件。

尝试打开一个包含 `mozilla.org` 的网站，比如 <https://developer.mozilla.org/zh-CN/> 就可以看到红色边框的效果。

#### 重载

尝试修改 `borderify.js` 的内容，比如把 red 改成 blue，然后点击 <about:debugging> 中的重载，可以立马看到变化的效果。

## 第二个拓展

### 概念引入 ：API

