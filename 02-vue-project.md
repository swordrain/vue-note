# 工程化的Vue.js开发

## 脚手架vue-cli

```
//安装
npm i vue-cli -g

用法： vue <命令> [选项]

命令
init  从指定模板中生成一个新的项目
list  列出所有的可用的官方模板
help [cmd]  显示所有[cmd]的帮助

选项
-h --help 输出用法信息
-V --version 输出版本号

vue list的结果
Available official templates:

  ★  browserify - A full-featured Browserify + vueify setup with hot-reload, linting & unit testing.
  ★  browserify-simple - A simple Browserify + vueify setup for quick prototyping.
  ★  pwa - PWA template for vue-cli based on the webpack template
  ★  simple - The simplest possible Vue setup in a single HTML file
  ★  webpack - A full-featured Webpack + vue-loader setup with hot reload, linting, testing & css extraction.
  ★  webpack-simple - A simple Webpack + vue-loader setup for quick prototyping.
```

## 深入vue-cli的工程模板
`webpack-simple`和`webpack`两个模板基于的`webpack`版本不同，暂时互相是不兼容的

### webpack-simple模板
```
├── README.md
├── index.html
├── package.json
├── src
│   ├── App.vue
│   ├── assets
│   │   └── logo.png
│   └── main.js
└── webpack.config.js
```

命名约定

1. 公共组建、指令、过滤器放在`src`目录下的`components` `directives` `filters`
2. 以使用场景命名Vue的页面文件
3. 当页面文件具有私有组件、指令和过滤器时，建立一个与页面同名的目录，页面文件更名为`index.vue`，将页面与相关的依赖文件放一起
4. 目录由全小写的名词、动名词或分词命名，用`-`进行分隔
5. Vue文件统一以大驼峰命名法命名，仅入口文件`index.vue`小写
6. 测试文件一律以测试目标文件名`.spec.js`命名
7. 资源文件一律以小写字符命名，用`-`分隔

### webpack模板
```
├── README.md
├── build
│   ├── build.js
│   ├── check-versions.js
│   ├── dev-client.js
│   ├── dev-server.js
│   ├── utils.js
│   ├── vue-loader.conf.js
│   ├── webpack.base.conf.js
│   ├── webpack.dev.conf.js
│   ├── webpack.prod.conf.js
│   └── webpack.test.conf.js
├── config
│   ├── dev.env.js
│   ├── index.js
│   ├── prod.env.js
│   └── test.env.js
├── index.html
├── package.json
├── src
│   ├── App.vue
│   ├── assets
│   │   └── logo.png
│   ├── components
│   │   └── Hello.vue
│   ├── main.js
│   └── router
│       └── index.js
├── static
└── test
    ├── e2e
    │   ├── custom-assertions
    │   │   └── elementCount.js
    │   ├── nightwatch.conf.js
    │   ├── runner.js
    │   └── specs
    │       └── test.js
    └── unit
        ├── index.js
        ├── karma.conf.js
        └── specs
            └── Hello.spec.js
```

### 构建工具
*编译开发环境*

```
npm run dev
```
* 加载环境变量
* 合并webpack配置
* 配置热加载
* 配置代理服务器
* 配置静态资源
* 加载开发服务器

*编译生产环境*

```
npm run build
```

## Vue工程的webpack配置与基本用法
略

## 基于Karma + Phantom + Mocha + Sinon + Chai的单元测试环境

`Karma`是测试加载器，作为自动化测试程序的入口

```
npm i karma -g
karma init
karma start
```
如果使用`webpack`模板，可以执行`npm run unit`启动`karma`  
`karma`通过`karma-`开头的一系列插件与其他组件进行集成

`PhantomJS`是一个无界面可脚本编程的Webkit浏览器引擎，它原生支持多种Web标准，如DOM操作、CSS选择器、JSON、Canvas以及SVG

`MonchaJS`是一个JS测试框架，一般用`Chai`提供断言，用`Sinon`提供Mock和Stub功能

## 基于NightWatch的端到端测试环境
NightWatch是基于Node.js的验收测试框架，使用Selenium WebDriver API将Web应用测试自动化。提供了简单的语法，支持使用JavaScript和CSS选择器来编写运行在Selenium服务器上的端到端测试

```
── e2e
   ├── custom-assertions
   │   └── elementCount.js
   ├── nightwatch.conf.js
   ├── runner.js
   └── specs
       └── test.js
```

在`webpack`模板里NightWatch相关的文件在`test/e2e`下面，其中`nightwatch.conf.js`是NightWatch的配置项

NightWatch配置可以分为以下三类

* 基本配置
* Selenium配置
* 测试环境配置

基本配置

* src_folders
* output_folder
* custom_commands_path
* custom_assertions_path
* page_objects_path
* globals_path
* selenium
* test_settings
* live_output
* disable_colors
* parallel_process_delay
* test_workers
* test_runner

Selenium配置

如果没有使用`vue-cli`，那么需要手工安装`npm i selenium-server`

配置项有

* start_process
* start_session
* server_path
* log_path
* port
* cli_args

cli_args包含

* webdriver.firefox.profile
* webdriver.chrome.driver
* webdriver.ie.driver

测试环境配置

* launch_url
* selenium_host
* selenium_port
* request_timeout_options
* silent
* output
* disable_colors
* screenshots
* username
* access_key
* proxy
* desiredCapabilities
* globals
* exclude
* log_screenshot_data
* use_xpath
* cli_args
* end_session_on_fail
* skip_testcases_on_fail
* output_filder
* persist_globals
* compatible_testcase_support
* detailed_output

使用PhantomJS加快速度

```
"test_settings": {
	"default": {
		...
	}
},
"phantom": {
	"desiredCapabilities": {
		"browserName": "phantomjs",
		"javascriptEnabled": true,
		"acceptSslCerts": true,
		"phantomjs.page.settings.userAgent": "Mozilla/5.0(Macintosh; Intel MacOS X 10_10_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/46.0.2490.90 Safari/537.36",
		"phantomjs.binary.path": "node_modules/phantomjs-prebuilt/bin/phantomjs"
	}
}
```

更干净的方式

```
var seleniumServer = require('selenium-server');
var phantomjs = require('phantomjs-prebuilt');

module.exports = {
	//...
	"selenium": {
		//...
		"server_path": seleniumServer.path,
	},
	"test_settings": {
		//...
		"phantom": {
			"desiredCapabilities": {
				//...
				"phantomjs.binary.path": phantomjs.path
			}
		}
	}
}
```
打开`runner.js`文件，改为

```
if (opts.indexOf('--env') === -1) {
	opts = opts.concat(['--env', 'phantom'])
}
```

