# 例说Vue.js

安装

```
npm i vue-cli -g
```
建立工程

```
vue init webpack-simple vue-todos
```

生成的目录结构

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

`*.vue`文件就对应一个Vue组件

```
//一个最简单的Vue组件模板
<template> 
	<div id="app"></div>
</template>
<style></style>
<script>
export default {
	name: 'app'
}
</script>
```
* `<template>`视图模板
* `<style>`组件样式表
* `<script>`组件定义

## 插值
使用`{{}}`方式进行插值，跟angular一样，如果要输出未被编码的文本，使用`{{{}}}`

```
export default {
	...
	data: {
		title: "vue-todos"
	}
}
//插值
<h1>{{title}}</h1>
```
`data`可以是一个返回Object对象的函数，也可以是一个对象属性

## 数据绑定
使用`v-for`进行数组或对象属性的遍历

```
<ul>
	<li v-for="(todo, index) in todos" :id="index">
		<label>{{index+1}}.{{todo.value}}</label>
	</li>
</ul>	
```

## 样式绑定
根据不同的样式处理方式，安装`webpack`的样式`loader`

```
import './assets/todos.less'
...
<li v-for="(todo, index) in todos" :class="{'checked': todo.done}">
	<label>{{index+1}}.{{todo.value}}</label>
</li>
```

`:prop="express"`就是`Vue`的属性绑定语法，`:class`绑定是布尔类型的绑定，`:style`绑定是样式配置项的绑定，跟angular也一样

通过`import`将样式文件导入是一种全局性的做法，可能引起冲突，可以使用`<style scoped>`，然后用CSS的`@import`导入样式表

```
<style scoped>
	@import './assets/todos.less'
</style>
```

## 过滤器
```
import moment from 'moment'
moment.locale('zh-cn')

export default {
	...
	filters: {
		date(val){
			return moment(val).calendar()
		}
	}
}
...
<time>{{todo.created|date}}</time>
```
