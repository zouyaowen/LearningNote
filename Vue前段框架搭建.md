## 安装NodeJS

```shell
node -v
v10.16.0
#可以使用下列任一命令安装
npm install -g @vue/cli
# OR
yarn global add @vue/cli
#检查其版本是否正确 (3.x)
vue --version
#使用 vue serve 和 vue build 命令对单个 *.vue 文件进行快速原型开发，不过这需要先额外安装一个全局的扩展
#npm install -g @vue/cli-service-global
#使用淘宝镜像就可以cnpm下载软件
npm install -g cnpm --registry=https://registry.npm.taobao.org
#创建项目，创建项目过程中不适用默认的配置自己选选中方式是使用空格键
vue create hello-world
#进入项目
cd hello-world
#运行项目
npm run serve
#install仅仅是安装
cnpm install axios
#install --save是安装并添加进当前的项目中
cnpm install --save axios vue-axios
cnpm install --save store
cnpm install --save vuex
cnpm install vue-router --save

#创建项目的第二种方式
vue ui

#在项目目录下添加文件.editorconfig,内容：
[*]
indent_style = space
indent_size = 4
trim_trailing_whitespace = true
insert_final_newline = true

#修改ESlint规则在package.json文件中的rules添加indent内容
	"eslintConfig": {
        "root": true,
        "env": {
            "node": true
        },
        "extends": [
            "plugin:vue/essential",
            "eslint:recommended"
        ],
        "rules": {
            "indent": [
                1,
                4
            ]
        },
        "parserOptions": {
            "parser": "babel-eslint"
        }
    }
    
    
#谷歌插件加载无效解决方案
extension_5_1_0_0.crx修改后缀名为rar
#解压成一个文件夹extension_5_1_0_0
#然后加载已解压的扩展程序——找到解压的文件夹即可

#或者下载最新版的插件Vue.js Devtools_5.1.1.crx直接拖即可



vue-ui项目、
#安装axios
cnpm i axios -S
```

## VUE3学习笔记

```shell
#安装node
#确认node版本
node -v 
v14.16.1
#与node一起安装的包管理工具
npm -v
#安装nrm可以使用国内的镜像源
npm install nrm -g
#查看镜像源
nrm ls
* npm -------- https://registry.npmjs.org/
  yarn ------- https://registry.yarnpkg.com/
  cnpm ------- http://r.cnpmjs.org/
  taobao ----- https://registry.npm.taobao.org/
  nj --------- https://registry.nodejitsu.com/
  npmMirror -- https://skimdb.npmjs.com/registry/
  edunpm ----- http://registry.enpmjs.org/ 
#使用淘宝镜像源
nrm use taobao
#再次查看镜像源
nrm ls
  npm -------- https://registry.npmjs.org/
  yarn ------- https://registry.yarnpkg.com/
  cnpm ------- http://r.cnpmjs.org/
* taobao ----- https://registry.npm.taobao.org/
  nj --------- https://registry.nodejitsu.com/
  npmMirror -- https://skimdb.npmjs.com/registry/
  edunpm ----- http://registry.enpmjs.org/
  
#删除旧的脚手架版本
npm uninstall vue-cli -g
# 或者如果使用yarn 则 yarn global remove vue-cli


#安装vue脚手架
npm install -g @vue/cli
#安装结果——————@vue/cli@4.5.12
#安装指定版本号脚手架
#npm install -g @vue/cli@4.5.12
#创建项目
vue create mark
#进入并运行项目
cd mark
npm run serve

  App running at:
  - Local:   http://localhost:8080/
  - Network: http://192.168.124.15:8080/

  Note that the development build is not optimized.
  To create a production build, run npm run build.
  

```























