```
ctrl + shift + k  删除当前行
alt + 上/下 上移或下移选中的行
ctrl + G 键入要导航到的行数
- \r\n是代码段里的换行
- tab键是代码段的光标位切换$1,$2之类
- ctrl + \ 是快速分屏
// 格式化 ，安装standard风格
// 全局eslint配置standard, 关闭自动fix。

>javaScript Standard Style: fix all auto-fixable Problems
npm list -g --depth=0 查看全局安装的包

```

- 我的vscode配置
```
{
    "git.confirmSync": false,
    "vsicons.projectDetection.autoReload": true,
    "window.zoomLevel": 0,
    "workbench.statusBar.visible": true,
    "workbench.iconTheme": "vscode-icons",
    "editor.minimap.enabled": true,
    "emmet.syntaxProfiles": {
      "vue-html": "html",
      "vue": "html"
    },
    "workbench.colorCustomizations": {
      "tab.activeBackground": "#282c34",
      "activityBar.background": "#282c34",
      "editorGroup.background": "#282c34",
      "sideBar.background": "#282c34"
    },
    "eslint.options": {
      "configFile": "/Users/yoshino/.eslintrc.js"
    },
    "eslint.validate": [
      "javascript",
      "javascriptreact",
      "html",
      "vue"
    ],
    "editor.tabSize": 2,
    // "eslint.autoFixOnSave": true,
    "search.exclude": {
        "**/.git": true,
        "**/.svn": true,
        "**/.hg": true,
        "**/.history": true,
        "**/.DS_Store": true,
        "**/bower_components": true,
        "**/jspm_packages": true,
        "**/node_modules": true
    },
    "files.associations": {
        "*.vue": "vue",
        // "*.vue": "html"
    },
    "files.exclude": {
        "**/.git": true,
        "**/.svn": true,
        "**/.hg": true,
        "**/.history": true,
        "**/.DS_Store": true,
        "**/bower_components": true,
        "**/jspm_packages": true,
        "**/node_modules": true
    },
    "prettier.semi": false,
    "prettier.singleQuote": true,
    "standard.validate": [
      "javascript",
      "javascriptreact",
      "vue"
    ],
    "gitlens.keymap": "alternate",
  }
```

- eslint配置

```
{
  "env": {
    "browser": true
  },
  "extends": "standard",
  "parserOptions": {
    "sourceType": "module"
  },
  "plugins": [
    "html"
  ],
  "rules": {
    "generator-star-spacing": 0
  }
}
```


代码段
```
{
	// Place your global snippets here. Each snippet is defined under a snippet name and has a scope, prefix, body and 
	// description. Add comma separated ids of the languages where the snippet is applicable in the scope field. If scope 
	// is left empty or omitted, the snippet gets applied to all languages. The prefix is what is 
	// used to trigger the snippet and the body will be expanded and inserted. Possible variables are: 
	// $1, $2 for tab stops, $0 for the final cursor position, and ${1:label}, ${2:another} for placeholders. 
	// Placeholders with the same ids are connected.
	// Example:
	// "Print to console": {
	// 	"scope": "javascript,typescript",
	// 	"prefix": "log",
	// 	"body": [
	// 		"console.log('$1');",
	// 		"$2"
	// 	],
	// 	"description": "Log output to console"
	// }
	"php Print to console": {
		"scope": "php",
		"prefix": "dd",
		"body": [
			"echo '<pre>';\r\nprint_r($$1);\r\necho '</pre>';",
			"$2"
		],
		"description": "php断点"
	},
	"js Print to console": {
		"scope": "javascript, typescript, html, vue",
		"prefix": "dd",
		"body": [
			"console.log('--------$1--------')",
			"console.log($2)",
			"$3"
		],
		"description": "js断点"
	},
	"row tag": {
		"scope": "javascript, typescript, html, vue, template",
		"prefix": "row",
		"body": [
			"<Row>",
			"$1",
			"</Row>"
		],
		"description": "row标签"
	},
	"coltag": {
		"scope": "javascript, typescript, html, vue, template",
		"prefix": "col",
		"body": [
			"<i-col>",
			"$1",
			"</i-col>"
		],
		"description": "col标签"
	},
	".vue init": {
		"scope": "javascript, typescript, html, vue, template",
		"prefix": "initv",
		"body": [
			"<template>",
			"  $1",
			"</template>",
			" ",
			"<script>",
			"export default {",
			"  data () {",
			"    return {",
			"      a: 1",
			"    }",
			"  },",
			"  methods: {",
			"  ",
			"  }",
			"}",
			"</script>"
		],
		"description": ".vue init"
	}
}
```