# 页面区块化与组件封装

## 组件化思想
拆分 - 复用

ref获取dom，在`mounted`钩子里
>跟React一样

```
<div class="swiper-container" ref="slider">

...

export default {
	mounted() {
		new Swiper(this.$refs.slider, {
			pagination: this.$refs.pagination,
			...
		}
	}
})

```

## 数据传递

>props跟React的props一样，data相当于React的state

```
//BookList.vue
export default {
	props: [
		'heading',
		'books'
	]
}

//Home.vue
<book-list :books="latestUpdated" heading="最新更新">
</book-list>
...
export default {
	date() {
		announcement: '...',
		latestUpdated: [...]
	},
	components: {
		BookList
	}
}
```

## 自定义事件
使用`$emit`

```
//BookList.vue片段
<div @click="$emit('onBookSelect', book)">

...

export default() {
	data() {
		...
	},
	methods: {
		preview(book){
			alert(book)
		}
	}
}

<book-list :books="latestUpdated" heading="最新更新" @onBookSelect="preview($event)">
</book-list>

```

## 服务端获取数据
使用`vue-resource`库

```
import Vue from 'vue'
import VueResource from 'vue-resource'
Vue.use(VueResource)

export default {
	data() {
		return {
			announcement: '',
			slides: [],
			latestUpdated: [],
			recommended: []
		}
	}
	
	created() {
		this.$http.get('/home').then((res)=> {
			for prop in res.body {
				this[prop] = res.body[prop]
			}
		}, (error) => {
			console.log(`${error}`)
		})
	}
```

可以在Vue的实例配置中对HTTP进行配置

```
new Vue({
	http: {
		root: '/api',
		headers: {}
	},
	...
})
```

`vue-resource`在`$http`对象上的方法

* get
* head
* delete
* jsonp
* post
* put
* patch

`options`对象参考

* url
* body
* headers
* params
* method
* timeout
* before
* progress
* credentials
* emulateHTTP
* enulateJSON

`response`对象参考

* url
* body
* headers
* ok
* status
* statusText
* text()
* json()
* blob()

## 复合型模板组件
使用`<slot></slot>`占位，默认的只能有一个，多于一个的通过`name`来区分
>类似于React的this.props.children

```
//dialog.vue
<template>
    <div class="dialog-wrapper"
         :class="{'open':is_open}">
        <div class="overlay" @click="close"></div>
        <div class="dialog">
            <div class="heading">
                <slot name="heading"></slot>
            </div>
            <slot></slot>
        </div>
    </div>
</template>

<script>
    import "./dialog.less"
    export default {
        data () {
            return {
                is_open: false
            }
        },
        methods: {
            open() {
                this.is_open = true
                this.$emit('dialogopen')
            },
            close() {
                this.is_open = false
                this.$emit('dialogclose')
            }
        }
    }
</script>

//使用
<template>
	<div>
		...
		<modal-dialog>
			<div slot="header">此处是header插槽的内容</div>
			<div>这个是自动默认的插槽的内容</div>
		</modal-dialog>
	</div>
</template>
```

