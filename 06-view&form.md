# 视图与表单的处理

集成UIKit

如果没有使用`webpack`模板，就要手动引入`url-loader`

在`main.js`文件中引入，这样使`UIKit`成为全局性的

**制作UIKit的Vue插件**

新建`uikit.js`文件

```
import 'jquery'
import 'uikit'
import 'uikit-css'

export default(Vue, options) {
	//向实例注入UIKit的对话框方法
	Vue.prototype.$ui = {
		alert: UIKit.modal.alert,
		confirm: UIKit.modal.confirm,
		prompt: UIKit.modal.prompt,
		block: UIKit.modal.block
	}
}
```

改写`main.js`

```
import UIKit from './uikit'
Vue.use(UIKit)
```

在使用的时候

```
methods: {
	delItem() {
		this.$ui.confirm("确认删除", () => {
			//...
		})
	}
```

其他全局话的处理

```
export default = (Vue, options) => {
	//1 注入全局化的方法
	Vue.myGlobalMethod = () => {
	
	}
	
	//2 组件注册
	Vue.component('html-editor', {
		HtmlEditor
	})
	
	//3 指令
	Vue.directive('sortable', {
		bind(el, binding, vnode, oldVnode){
			//...
		}
	})
	
	//4 注入mixin
	Vue.mixin({
		created: function() {
			//...
		}
	}
	
	//5 扩展实例
	Vue.prototype.$ui = {}
}
```

防止出错的一个最佳实践，在`webpack.config.js`中对全局变量进行改写

```
plugins: [
	new webpack.ProvidePlugin({
		$: 'jquery',
		jQuery: 'jquery',
		"window.jQuery": 'jquery',
		"window.$": 'jquery'
	})
]
```

`Vue2`也是集成了`VirtualDom`，`template`最终会被编译成一个`Render`对象，`compile`全局方法就是讲HTML模板字符串编译成一个`Render`对象

```
let renderObject = Vue.compile(`<div>
	<h1>模板</h1>
	<p v-if="message">
		{{message}}
	</p>
	<p else>
		尚无消息
	</p>
</div>`)
//编译结果
//{staticRenderFns: Array(0), render: function}
//render方法内容
//function anonymous() {
with(this){return _h('div',[_h('h1',["模板"])," ",(message)?_h('p',["\n\t\t"+_s(message)+"\n\t"]):_e()," ",_h('p',{attrs:{"else":""}},["\n\t\t尚无消息\n\t"])])}
}
```
>类似于React编译JSX

可以使用`render`方式来写一个纯JS的组件

```
export default {
	name: 'UkButton', //组件名必须
	props: [],
	data: {},
	method: {},
	...
	render(createElement) {
		return createElement('button', {
			class:{
				'uk-button': true
			}
		})
	}
}
```

`createElement`可以用来构造DOM，实例化Vue组件

```
createElement(tag, data, childre)
data对象可以包含
class
style
attrs
props
domProps
on
nativeOn
directives
slot
key
ref
```

`Vue`也可以集成`JSX`，用`JSX`的方式来写`template`

表单验证，`vue-validator`还不能支持`Vue2`，现在使用`vee-validate`

开发时如果要设置代理避免跨域，可以在`config/index.js`里配置(集成了http-proxy-middleware)

```
dev:{
	proxyTable: {
		'/api':{
           	 	target: "http://api.douban.com",
            		changeOrigin: true,
            		pathRewrite: {
              			'^/api': '/'
            		}
        	}
	}
}
```

`/api`是匹配模式

* ** - 匹配所有模式
* ** / *.html - 匹配html结尾的请求
* /*.html - 匹配根目录下html结尾的请求
* /api/** /*.html - 匹配/api路径下任意html结尾的请求

其他配置选项有(具体查看micromatch)
* target
* forward
* agent
* ssl
* ws
* xfwd
* secure
* toProxy
* prependPath
* ignorePath
* localAddress
* changeOrigin
* auth
* hostRewrite
* autoRewrite
* protocolRewrite
* cookieDomainRewrite
* headers
* proxyTimeout

如果要进行后端服务仿真，到`build/dev-server.js`里进行修改
