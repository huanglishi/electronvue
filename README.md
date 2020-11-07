# electron+vue-cli3+sqlist开发电脑端桌面应用
安装说明：
#### 1.首先使用vue-cli创建项目
选取一个项目存放的路径，然后开始创建项目 例如：
vue create electronname
输入完上述命令之后进入vue项目的创建过程。出现以下内容

```javascript
Vue CLI v3.8.4
? Please pick a preset: (Use arrow keys)
  default (babel, eslint) 
> Manually select features 

```
第一个选项是 “default” 默认，只包含babel和eslint
第二个选项是 “Manually select features”自定义安装

选择自定义安装，进入下一步选择

```javascript
? Check the features needed for your project: (Press <space> to select, <a> to t
oggle all, <i> to invert selection)
❯◉ Babel
 ◯ TypeScript
 ◯ Progressive Web App (PWA) Support
 ◉ Router
 ◉ Vuex
 ◉ CSS Pre-processors
 ◉ Linter / Formatter
 ◯ Unit Testing
 ◯ E2E Testing
```
这里我们选择

babel（高级的语法转换为 低级的语法）
Router（路由）
Vuex（状态管理器）
CSS Pre-processors（css预处理器）
Linter / Formatter（代码风格、格式校验）
然后进入下一步

```
? Use history mode for router? (Requires proper server setup for index fallback 
in production) (Y/n)  n
```
这一步是设置router是否使用history模式，这里我们选n，接着进入下一步

```
? Pick a CSS pre-processor (PostCSS, Autoprefixer and CSS Modules are supported 
by default): (Use arrow keys)
 ❯ Sass/SCSS (with dart-sass) 
  Sass/SCSS (with node-sass) 
  Less 
  Stylus 
```
这里是设置css预处理模块，在这里我要强调一下，不需要乱选，选择我们熟悉的一种，在这里我们选择 Sass/SCSS，然后进入下一步

```
? Where do you prefer placing config for Babel, PostCSS, ESLint, etc.? 
  In dedicated config files 
  In package.json 
```
这一步是询问 babel, postcss, eslint这些配置是单独的配置文件还是放在package.json 文件中，这里我们选择“In package.json”，然后进入下一步

```
? Save this as a preset for future projects? (y/N) N
```
这一步是询问时候以后创建项目是否也采用同样的配置，这里我们选N。到目前为止，vue项目是创建完成了，我们等待项目下载依赖包，等项目构建完毕我们开始集成electron

#### 2.使用electron-builder集成electron
进入项目根目录（electronname），然后执行下列命令：
```
vue add electron-builder
```
这个时候会安装electron-builder的依赖，可能比较耗费时间，请大家耐心等待，安装完成后会出现以下选型：
请选择最大的版本
至此，所有的安装都已经完成了，接下来我们就可以运行程序看效果了。
#### 3.运行程序
```
npm run electron:serve
```
在启动的时候，会启动很久，并出现以下信息

```
INFO  Launching Electron...
Failed to fetch extension, trying 4 more times
Failed to fetch extension, trying 3 more times
Failed to fetch extension, trying 2 more times
Failed to fetch extension, trying 1 more times
```
这是在安装vuejs devtools，由于网络问题，一直安装不上。重试5次之后就会自动跳过并且启动程序。
您可把background.js的Install Vue Devtools安装代码删掉手动引入
  ```
   try {
      const { session } = require("electron");
      const path = require("path");
      session.defaultSession.loadExtension(
        path.resolve(__dirname, "../devTools/chrome") 
      ); 
    } catch (e) {
      console.error('Vue Devtools failed to install:', e.toString())
    }
  ```
