---
layout: post
catalog: true
tags:
  - Electron
---

如何将一个Vue项目转为Electron应用

**vue add electron-builder**

`vue add electron-builder` 是一个 Vue CLI 插件命令，用于将 Electron 构建工具集成到你的 Vue 项目中。具体来说，这个命令会：

1. **安装依赖**：安装 `electron-builder` 和其他必要的依赖项。
2. **配置项目**：在项目根目录下生成或更新 `vue.config.js` 和 `package.json` 文件，以配置 Electron 构建相关的设置。
3. **添加脚本**：在 `package.json` 中添加构建和打包 Electron 应用的脚本。

**npm run electron:build**

`npm run electron:build` 是一个 npm 脚本命令，用于执行 Electron 应用的构建和打包过程。具体来说，这个命令会：

1. **读取配置**：读取 `vue.config.js` 和 `package.json` 中的 Electron 构建配置。
2. **打包应用**：使用 `electron-builder` 工具将 Vue.js 应用打包成可执行文件（例如 `.exe`、`.dmg`、`.appimage` 等）。
3. **生成安装包**：根据配置生成不同平台的安装包，如 Windows 的 `.exe` 文件、macOS 的 `.dmg` 文件、Linux 的 `.AppImage` 文件等。

这个命令通常在开发完成后，用于生成最终的可分发版本。