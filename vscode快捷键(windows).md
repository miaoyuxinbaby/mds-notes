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
