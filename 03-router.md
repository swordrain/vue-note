# 路由与页面间导航

## vue-router
```
//安装
npm i vue-router
//使用
// config/router.js
import Vue from 'vue'
import VueRouter from 'vue-router'
Vue.use(VueRouter)
...

export default = new VueRouter({
	mode: 'history',
	base: __dirname,
	routers: [
		{path: '/home', component: Home},
		{path: '/explorer', component: Explorer},
		{path: '/cart', component: Cart},
		{path: '/me', component: Me},
	]
})

// main.js
import Vue from 'vue'
import App from './App.vue'
import router from './config/routes'

new Vue({
	el: '#app',
	router,
	render: h => h(App)
})
```

## 路由的模式
* Hash - 使用URL hash作为路由，支持所有浏览器
* History - 依赖HTML5 History API和服务器配置
* Abstract - 支持所有JavaScript运行环境，如Node.js服务器端，如果发现没有浏览器环境的API，会强制进入这个模式

## 路由与导航
处理导航与自动渲染逻辑的两个指令标签
* <router-view> - 渲染路径匹配到的视图组件，可以内嵌自己的<rouoter-view>
* <router-link> - 支持用户在具有路由功能的应用中导航

一个良好的实践是使用命名路由方式

```
export default = new VueRouter({
	mode: 'history',
	base: __dirname,
	routers: [
		{name: 'Home', path: '/home', component: Home},
		{name: 'Explorer', path: '/explorer', component: Explorer},
		{name: 'Cart', path: '/cart', component: Cart},
		{name: 'Me', path: '/me', component: Me},
	]
})
```
在`<router-link>`里

```
...
<router-link :to="{name:'Home'}" tag="li">
	<div><img src="../assets/images/home.sve"></div>
	<div>Home</div>
</router-link>
```
`tag="li"`表示会用`<li>`包裹`<router-link>`，如果不指定，会用默认的`<a>`

**带参数路由**

```
routers: [{
	name: 'BookDetails',
	path: '/books/:id',
	component: BookDetails
}]	
```
在`<router-link>`里要加入`params`属性
```
<router-link to="{name: 'BookDetails', params: {id: 1}}">
	...
</router-link>
```
读取id参数
```
export default {
	created(){
		const bookId = this.$router.params.id
	}
}
```
从`/books/1`导航到`/books/2`，原来的组件实例会被复用，但组件的生命周期钩子不会再被调用，如果想要监听路由参数的变化
```
export default {
	template: '...',
	watch: {
		'$route' (to, from) {
			...
		}
	}
}
```
如果是`/path?para=value`的方式，可以使用`$router.query.参数名`方式读取，`vue-router`还提供了常亮参数定义`meta`，用法同前

**嵌套路由**

```
export default = new VueRouter({
	mode: 'history',
	base: __dirname,
	linkActiveClass: 'active',
	routers: [{
		name: 'Main',
		path: '/',
		component: Main,
		children: [
			{name: 'Home', path: 'home', component: Home},
			{name: 'Explorer', path: 'explorer', component: Explorer},
			{name: 'Cart', path: 'cart', component: Cart},
			{name: 'Me', path: 'me', component: Me},		]
	}]
})
```

**切页动效**

四个CSS类名在enter/leave的过渡中切换
* CSS类名-enter  定义进入过渡的开始状态。在元素被插入时生效，在下一帧移除
* CSS类名-enter-active  定义进入过渡的结束状态，在元素被插入时生效，在transition/animation完成之后移除
* CSS类名-leave  定义离开过渡的开始状态，在离开过渡被触发时生效，在下一帧移除
* CSS类名-leave-active  定义离开过渡的结束状态，在离开过渡被触发时生效，在transition/animation完成之后移除

```
<template>
	<transition name="slide-fade">
		<router-view>
		</router-view>
	</transition>
</template>

<style>
	.slide-fade-enter-active {
		transition: all .3s ease;
	}
	.slide-fade-leave-active {
		transition: all .3s cubic-bezier(1.0, 0.5, 0.8, 1.0);
	}
	.slide-fade-enter, .slide-fade-leave-active {
		transform: translateX(-430px);
		opacity: 0;
	}
```

## 导航状态样式
默认情况下当`<router-link>`对应的路由匹配成功时，会自动设置`class`属性为`.router-link-active`，如果想要自定义类名，通过`active-class`属性设置
```
<router-link :to="{name:'Home'}" tag="li" active-class="active">
	<div><img src="../assets/images/home.sve"></div>
	<div>Home</div>
</router-link>
```
也可以在配置路由的时候全局设置

```
export default = new VueRouter({
	mode: 'history',
	base: __dirname,
	linkActiveClass: 'active',
	...
})
```

**精确匹配与包含匹配**

当前路径为`/home`时，`<router-link to="/">`也会被匹配并设置上激活类，此时需要使用`exact`

```
<router-link :to="{name:'Home'}" exact>
```

## History的控制
Vue实例内有一个`$router`对象，这个对象有三个方法，`<router-link>`则是用两种属性对应三个方法的调用

| router方法       | 属性           | 说明  |
| ------------- |-------------|---|
| push()      | - | 默认调用此方法
| append()      | append      |   将目标URL追加到当前URL下 |
| replace() | replace      |    以目标URL替换现有的URL |

```
<router-link :to="{name:'Home'}" replace>
<router-link :to="{name:'Home'}" append>
```

## 关于Fallback
服务器端应该把所有的请求都转发给`/index.html`，否则会在服务器端触发`404`错误

在`webpack.config.js`中已经默认开启了

```
devServer: {
	historyApiFallback: true
}
```
其他服务器的配置

**Apache**

```
<IfModule mod_rewrite.c>
	RewriteEngine On
	RewirterBase /
	RewriteRule ^index\.html$ = [L]
	RewriteCond %{REQUEST_FILENAME} !-f
	RewriteCond %{REQUEST_FILENAME} !-d
	RewriteRule . /index.html [L]
</IfModule>
```

**Nginx**

```
location / {
	try_files $uri $uri/ /index.html;
}
```

**Node.js(Express)**

`https://github.com/bripkens/connect-history-api-fallback`

**Flask(Python)**

```
from flask import Flask, render_template
app = Flask(__name__)
@app.route('/')
def index():
	return render_template('index'.html)
	
@app.app_errorhandler(404)
def api_fall_back(e):
	return index()
```

**Ruby on Rails**

```
# ~/config/routes.rb
root 'home#index'

# ...

match 'path*', :to 'home#index'
```

`404`错误此时应该由前端实现

```
const router = new VueRouter({
	mode: 'history',
	routes: [
		{path: '*', component: NotFoundComponent}
	]
})
```