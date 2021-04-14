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
		"48": "icons/border-48.png",
		"96": "icons/border-96.png"
	},
	"content_scripts": [
		{
			"matches": ["*://*.mozilla.org/*"],
			"js": ["borderify.js"]
		}
	]
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

必要的操作是提供 48\*48 以及 96\*96 的图标，以获得更高清的显示。

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

然后打开 Firefox，进入 `about:debugging` 链接 - 此 Firefox - 临时载入附加组件，然后选中 `manifest.json` 即可加载组件。

尝试打开一个包含 `mozilla.org` 的网站，比如 <https://developer.mozilla.org/zh-CN/> 就可以看到红色边框的效果。

#### 重载

尝试修改 `borderify.js` 的内容，比如把 red 改成 blue，然后点击 <about:debugging> 中的重载，可以立马看到变化的效果。

## 第二个拓展

### 编写

#### manifest.json

```json
{
	"manifest_version": 2,
	"name": "Beastify",
	"version": "1.0",
	"description": "Adds a browser action icon to the toolbar. Click the button to choose a beast. The active tab's body content is then replaced with a picture of the chosen beast. See https://developer.mozilla.org/en-US/Add-ons/WebExtensions/Examples#beastify",
	"homepage_url": "https://github.com/mdn/webextensions-examples/tree/master/beastify",
	"icons": {
		"48": "icons/beasts-48.png"
	},
	"permissions": ["activeTab"],
	"browser_action": {
		"default_icon": "icons/beasts-32.png",
		"default_title": "Beastify",
		"default_popup": "popup/choose_beast.html"
	},
	"web_accessible_resources": ["beasts/frog.jpg", "beasts/turtle.jpg", "beasts/snake.jpg"]
}
```

#### beastify.js

新建一个 `content_scripts` 文件夹，在其中创建 `beastify.js` 文件：

```js
(function () {
	/**
	 * Check and set a global guard variable.
	 * If this content script is injected into the same page again,
	 * it will do nothing next time.
	 */
	if (window.hasRun) {
		return;
	}
	window.hasRun = true;

	/**
	 * Given a URL to a beast image, remove all existing beasts, then
	 * create and style an IMG node pointing to
	 * that image, then insert the node into the document.
	 */
	function insertBeast(beastURL) {
		removeExistingBeasts();
		let beastImage = document.createElement("img");
		beastImage.setAttribute("src", beastURL);
		beastImage.style.height = "100vh";
		beastImage.className = "beastify-image";
		document.body.appendChild(beastImage);
	}

	/**
	 * Remove every beast from the page.
	 */
	function removeExistingBeasts() {
		let existingBeasts = document.querySelectorAll(".beastify-image");
		for (let beast of existingBeasts) {
			beast.remove();
		}
	}

	/**
	 * Listen for messages from the background script.
	 * Call "beastify()" or "reset()".
	 */
	browser.runtime.onMessage.addListener((message) => {
		if (message.command === "beastify") {
			insertBeast(message.beastURL);
		} else if (message.command === "reset") {
			removeExistingBeasts();
		}
	});
})();
```

#### icons & beasts

创建 `icons` 和 `beasts` 文件夹，将对应的图片内容放入相应文件夹。

### 测试拓展

同样地进入 `about:debugging` 页面载入插件，选中 `menifest.json` 文件。

此时浏览器的右上角会显示出一个图标![img](/02-beastify/icons/beasts-32.png)

打开新的标签页，点击右上角的图标来进行测试。

## 第三个拓展

### manifest.json

```json
{
	"manifest_version": 2,
	"name": "selection-to-clipboard",
	"description": "Example of WebExtensionAPI for writing to the clipboard",
	"version": "1.0",
	"homepage_url": "https://github.com/mdn/webextensions-examples/tree/master/selection-to-clipboard",

	"icons": {
		"48": "icons/clipboard-48.png"
	},

	"content_scripts": [
		{
			"matches": ["<all_urls>"],
			"js": ["content-script.js"]
		}
	]
}
```

### content-script.js

```js
/*
copy the selected text to clipboard
*/
function copySelection() {
	var selectedText = window.getSelection().toString().trim();

	if (selectedText) {
		document.execCommand("Copy");
	}
}

/*
Add copySelection() as a listener to mouseup events.
*/
document.addEventListener("mouseup", copySelection);
```

## 参考资料

官方文档：<https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions>
