# 视图与表单的处理

## 集成UIKit
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

