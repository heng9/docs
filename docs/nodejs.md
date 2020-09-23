# nodejs
* npm 配置
```
npm config set prefix "D:\nodejs\node_global"
npm config set cache "D:\nodejs\node_cache"
```
	
* 由于修改了目录的位置，需要加入环境变量
```
NODE_PATH:  D:\nodejs\node_global\node_modules
Path:       D:\nodejs\node_global
```

* 安装淘宝代理
```
npm install -g cnpm --registry=https://registry.npm.taobao.org
```
        
    
* 模块安装
```
npm install -g
npm install --save / npm install -S         作为dependencies依赖
npm install --save-dev / npm install -D     作为devDepandencies依赖
```
	


# express
* 安装express模块
```
npm install -g express
npm install -g express-generator
```

	
* 创建express工程
```
express project_name
```
		
* express项目结构
	* app.js: 项目入口
	* node_modules: 存放项目的依赖库
	* public: 静态文件如 css,js,img
	* routes: 路由文件
	* Views: 页面文件(Ejs或者jade的模板，默认是jade)
	* package.json: 项目依赖配置及开发者信息


# bower
```
npm install bower -g
注意: 需要安装git，并配置环境变量，还需要配置模块 npm install serve-static --save
在app.js中
		var serveStatic = require('serve-static')
		app.use(serveStatic('bower_components')
```