# Vuex状态管理

## Vuex的基本结构

单向数据流

* state
* view
* actions

安装

```
npm i vuex
```
在`main.js`全局配置文件引入并配置

```
import Vue from 'vue'
import App from './App.vue'
import store from './store'

new Vuew({
	el: '#app',
	store,
	render: h=>h(App)
})
```
在每个组件实例内，都可以用`this.$store`来引用这个`store`对象

```
//src/store/index.js

import Vue from 'vue'
import Vuex from 'vuex'

const debug = process.env.NODE_ENV !== 'production'

Vue.use(Vuex)

export default new Vuex.Store({
	strict: debug,
	plugin: debug? [createLogger()]: []
})
```

一个完整的`Store`定义的结构

```
export default {
	state: {},
	actions: {},
	getters: {},
	mutations: {},
	modules: {},
	strict: true,
	plugin: [],
}
```

* state - Vuex store实例的根状态对象，用于定义共享的状态变量，就是Vue实例中的data
* getters - 读取器，外部程序通过它获取变量的具体值，或者在取值前做一些计算
* actions - 用于向store发出调用通知，执行本地或者远程的某个操作
* mutations - 修改器，用于修改state中定义的状态变量
* modules - 模块，向store注入其他子模块，可以将其他模块以命名空间的方式引用
* strict - 用于设置Vuex运行模式，true为调试模式，false为生产模式
* plugin - 向Vuex加入运行期插件

## State和Getter
数据统一放在`state`变量内，替代`data`和`computed`

```
export default {
	state: {
		announcements: [],
		promotions: [],
		recommended: []
	},
	getters:{
		totalPromotions: state => state.promitions.length,
		totalRecommended: state => state.recommended.length
	}
}
```
使用时

```
import {mapGetters} from 'vuex'
export default {
	computed: {
		...mapState([
			'announcements', //相当于this.$store.state. announcements
			'promotions',
			'recommended',
		]),
		...mapGetters([
			'promitionsCount',//相当于this.$store.getters.promitionsCount
			'recommendedCount'
		])
	}
}
```

## Action
操作`state`的方法

```
export default {
	state: {
		announcements: [],
		promotions: [],
		recommended: []
	},
	getters:{
		totalPromotions: state => state.promitions.length,
		totalRecommended: state => state.recommended.length
	},
	actions: {
		getStarted(context) {
			Vue.http.get('/api/get-start', (res)=>{
				context.commit('startedDataReceived', res.body)
			})
		}
	}
}
```

`context`对象与`store`实例结构相同，它具有如下属性

* state - 等于store.state，若在模块中则为局部状态
* rootState - 等于store.state，只存在于模块中
* commit - 等于store.commit，用于提交一个mutation
* dispatch - 等于store.dispatch，用于调用其他action
* getters - 等于store.getters，获取store中的getters

## Mutaion
只用`mutation`来修改状态

```
export default {
	state: {
		announcements: [],
		promotions: [],
		recommended: []
	},
	getters:{
		totalPromotions: state => state.promitions.length,
		totalRecommended: state => state.recommended.length
	},
	actions: {
		getStarted(context) {
			Vue.http.get('/api/get-start', (res)=>{
				context.commit('startedDataReceived', res.body)
			})
		}
	},
	mutations: {
		startedDataReceived(state, {started_data}) {
			state.announcements = started_data.announcements
			state.promotions = started_data.promotions
			state.recommended = started_data.recommended
			
		}
	}
}
```
`startedDataReceived`字符串也可以用`mutation-type.js`文件来集中管理

使用时

```
export default {
	computed: {
		...mapState([
			'announcements', //相当于this.$store.state. announcements
			'promotions',
			'recommended',
		]),
		...mapGetters([
			'promitionsCount',//相当于this.$store.getters.promitionsCount
			'recommendedCount'
		])
	},
	methods: {
		...mapActions(['getStarted'])
	},
	created() {
		this.getStarted()
	}
}
```

## 子状态和模块
`state`太大的话就分`module`

结构

```
└── store
    ├── actions.js
    ├── index.js
    ├── modules
    │   ├── cart.js
    │   └── products.js
    ├── mutation-type.js
    └── mutations.js
```

入口文件就变成

```
import Vue from 'vue'
import Vuex from 'vuex'
import createLogger from 'vuex/dist/logger'
import * as actions from './actions'
import * as getters from './getters'
import cart from './modules/cart'
import products from './modules/products'

const debug = process.env.NODE_ENV !== 'production'

Vue.use(Vuex)

export default new Vuex.Store({
	modules: {
		cart,
		products
	},
	actions,
	getters,
	strict: debug,
	plugin: debug? [createLogger()]: []
})
```

`cart.js` 

```
import Vue from 'vue'
import * as apis from '../apis'
import * as types from '../mutation-types'

const state = {
	all: []
}

const mutations = {
	[type.SET_CART_ITEMS](state, cart) {
		state.all = cart.items
	}
}

const actions = {
	getItems (context) {
		Vue.http.get(apus.GET_CART_ITEMS)
			.then(res => {
				context.commit(types.SET_CART_ITEMS, res.body.data)
			})
	}
}

export default {
	state,
	mutations,
	actions
}

```

结构变化后，状态引用就变成，而其他不变

```
this.$store.state.cart.all
```

使用`registerModule`方法动态注册

```
store.registerModule('books', {
	...
})
```

`unregisterModule`可以用来卸载动态模块，但不能卸载静态模块（创建store时声明的模块）

## 用服务分离外部操作
创建类似于`Angular.js`里`service`功能的代码来组织逻辑
