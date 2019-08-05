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

