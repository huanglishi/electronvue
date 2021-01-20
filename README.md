# electron+vue-cli3+sqlist开发电脑端桌面应用
安装说明：
#### 1.首先使用vue-cli创建项目
选取一个项目存放的路径，然后开始创建项目 例如：
```
vue create electronname
```
输入完上述命令之后进入vue项目的创建过程。出现以下内容

```javascript
Vue CLI v4.5.8
? Please pick a preset: (Use arrow keys)
  Default ([Vue 2] babel, eslint)
  Default (Vue 3 Preview) ([Vue 3] babel, eslint)
>  Manually select features    

```
第一个选项是 “default” 默认，只包含babel和eslint
第二个选项是 “Manually select features”自定义安装

选择自定义安装{Manually select features }，进入下一步选择

```javascript

? Check the features needed for your project:
 ( ) Choose Vue version
 (*) Babel
 ( ) TypeScript
 ( ) Progressive Web App (PWA) Support
 (*) Router
 (*) Vuex
 (*) CSS Pre-processors
>( ) Linter / Formatter
 ( ) Unit Testing
 ( ) E2E Testing                                                                                                                             
```
这里我们选择

babel（高级的语法转换为 低级的语法）
Router（路由）
Vuex（状态管理器）
CSS Pre-processors（css预处理器）
然后进入下一步

```
? Use history mode for router? (Requires proper server setup for index 
fallback in production) (Y/n)    n
```
这一步是设置router是否使用history模式，这里我们选n，接着进入下一步

```
? Pick a CSS pre-processor (PostCSS, Autoprefixer and CSS Modules are supported by default): (Use arrow keys)
> Sass/SCSS (with dart-sass)
  Sass/SCSS (with node-sass)
  Less
  Stylus  
```
这里是设置css预处理模块，在这里我要强调一下，不需要乱选，选择我们熟悉的一种，在这里我们选择 Sass/SCSS，然后进入下一步

```
? Where do you prefer placing config for Babel, ESLint, etc.?
  In dedicated config files
> In package.json                                                                                                                            
```
这一步是询问 babel, postcss, eslint这些配置是单独的配置文件还是放在package.json 文件中，这里我们选择“In package.json”，然后进入下一步

```
? Save this as a preset for future projects? (y/N) N
```
这一步是询问时候以后创建项目是否也采用同样的配置，这里我们选N。到目前为止，vue项目是创建完成了，我们等待项目下载依赖包，等项目构建完毕我们开始集成electron

#### 2.使用electron-builder集成electron
进入项目根目录（electronname），然后执行下列命令：
```
#进入项目目录
cd electronname
#再执行下面命令安装electron
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
#### 5.安装sqlit3本地数据库
```
npm install sqlite3 --save
#如果下不来请用淘宝
cnpm install sqlite3 --save
#如果还不行，请用下面命令多刷几次
npm cache clear -force
```
注意：需要在fs或sqlite渲染器过程中使用本机模块，vue.config.js必须配置 nodeIntegration: true；如下：
```
module.exports = {
  pluginOptions: {
    electronBuilder: {
      nodeIntegration: true
    }
  }
}
```
数据文件路径问题
```
const path = require('path')
const webpack = require('webpack')
module.exports = {
 configureWebpack: {
        plugins:[
            new webpack.DefinePlugin({
                __resources: `"${path.join(__dirname, './resources').replace(/\\/g, '\\\\')}"`,
            }),
        ]
    }
}
```
使用sqlit小样
```
   var sqlite3 = require('sqlite3').verbose();
   console.log("sqlite3测试：",sqlite3)
      const path = require("path");
      let _that = this;
       let lsrc=path.join(__resources, "/data/local.db")
      console.log("路径：",lsrc)
      // let ruing=path.join(remote.app.getPath('userData'), '/data.db');
     const db = new sqlite3.Database(lsrc);
      db.run("create table test( id INTEGER PRIMARY KEY AUTOINCREMENT,name varchar(15))", function() {
      let addname="测名称：";
      db.run('insert into test (name) values("'+addname+'")', function() {
        db.all("select * from test order by id desc limit 8 ", function(err, res) {
          if (!err) {
            _that.list=res
            console.log(JSON.stringify(res));
          } else {
            console.log(err);
          }
        });
      });
    });
```

#### 6.打包问题
```
#打包命令
npm run electron:build
```
###### 如果打包无法下载依赖包：本利使用electron9.3.3讲解

问题： 包下载出错的包：如electron-v9.3.3-win32-x64.zip用其他方式手动下载放到下面路径的位置 C:\Users\*****\AppData\Local\electron\Cache 根据提示同理下载其他包 需要注意的是，不仅要下载这个压缩包，还要把对应的SHASUMS256.txt-文件也下载下来放进去； 
请到这里去找对应的版本下载地址：https://npm.taobao.org/mirrors/electron 这个是国内阿里管理的包
###### 6.1下载包

------------
1.electron-v9.3.3-win32-x64.zip [下载地址](https://npm.taobao.org/mirrors/electron/9.3.3/electron-v9.3.3-win32-x64.zip "下载地址")

2.electron-v9.3.3-win32-ia32.zip[下载地址](https://npm.taobao.org/mirrors/electron/9.3.3/electron-v9.3.3-win32-ia32.zip "下载地址")
###### 6.2其他路径问题

------------

1.C:\Users\*****\AppData\Local\electron-builder\cache\winCodeSign

2.C:\Users\***\AppData\Local\electron-builder\cache\winCodeSign\winCodeSign-2.4.0\winCodeSign

打包配置 vue.config.js完整内容 [请前往查看](https://github.com/huanglishi/electronvue/blob/main/vue.config.js "请前往查看")
### 在您方便时候也可以在右上角给我点一下 `⭐ Star` 鼓励一下~哦
###  开发过程又是什么问题可加 QQ:[504500934](https://ynjiyuan.com "504500934") 进行交流
## 如果可以帮助您，您也可以赞助一下喝杯茶
赞助地址：[赞助一下喝杯茶](https://npm.taobao.org/mirrors/electron/9.3.3/electron-v9.3.3-win32-ia32.zip "赞助一下喝杯茶")
![Pandao editor.md](https://honey.ynjiyuan.com/wxpayqrcode.png "Pandao editor.md")
