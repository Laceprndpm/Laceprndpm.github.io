---
title: Hexo+anzhiyu入门
date: 2024-11-9 10:17:00
cover: https://bu.dusays.com/2023/05/13/645fa3cf90d70.webp
categories:
- Hexo
tags:
- 入门
- 博客
---
如何使用[Hexo](https://hexo.io/zh-cn/docs/index.html)制作个人博客
~~虽然总体看看文档就可以,但本人走了不少弯路子,所以还是写一下...~~
# 1.配置Hexo
## 1.1安装Node.js
访问[Node官网](https://nodejs.org/zh-cn),安装Node.js
不建议通过Winget或其他[包管理器](https://nodejs.org/zh-cn/download/package-manager),直接用Windows的图形化安装界面即可
安装后在powershell中输入
```powershell
node --version
```
可以看到
```powershell
v22.11.0
```
(你也可以选择其他版本)
## 1.2安装git
[git](https://git-scm.com/downloads/win)也是通过图形化安装界面安装,安装后即可有git和bash环境,可以参考VScode的git配置教程
## 1.3安装Hexo
在powershell中输入
```powershell
npm install -g hexo-cli
```
~~理论上也可以创建项目文件夹,进入文件夹后局部安装hexo,再初始化,但我不会~~
## 1.4新建blog项目
```powershell
hexo init blog
```
执行上述命令后，你可以进入 blog 文件夹并继续配置和使用 Hexo:
```powershell
cd blog
npm install
hexo server
```
此时hexo就可以正常使用,并且可以访问 (http://localhost:4000/) 来看示例
# 2.安装anzhiyu的主题
## 2.0了解hexo的结构
blog文件夹里,需要关心的只有**themes,source,_config.yml**,以及后续可能的**_config.你下载的themes.yml**
themes文件夹里将会存放你后续下载的主题
source文件夹里是你网页的.md源文件
_config.yml是你的配置
## 2.1下载anzhiyu
用git克隆anzhiyu的github仓库
```powershell
git clone -b main https://github.com/anzhiyu-c/hexo-theme-anzhiyu.git themes/anzhiyu
```
安装渲染插件
```powershell
npm install hexo-renderer-pug hexo-renderer-stylus --save
```
应用主题,将landscape改成anzhiyu
```yml
# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: anzhiyu
```
随后你就可以重启heox server,再次连上4000端口,看到主题更改后的效果
## 2.2开始使用
到这里就可以参考 https://docs.anheyu.com/initall.html 的教程
## 2.3补充
### (1)tags和categories
tags和categories也是在front-matter中设置
示例:
```md
---
title: Test
date: 2024-11-9 10:47:00
categories:
- Hexo
tags:
- 入门
- 博客
---
```
# 参考网站
以下是一些有用的参考网站，可以帮助你更好地使用 Hexo 和 anzhiyu 主题：

- [Hexo 官方文档](https://hexo.io/zh-cn/docs/)
- [anzhiyu 主题文档](https://docs.anheyu.com/initall.html)
- [Node.js 官方网站](https://nodejs.org/zh-cn/)
- [Git 官方网站](https://git-scm.com/)
- [Hexo 插件](https://hexo.io/plugins/)
- [Hexo 主题](https://hexo.io/themes/)