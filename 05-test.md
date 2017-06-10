# Vue的测试与调试技术

## Mocha入门

**基本的测试骨架**

单元测试文件都放在`test/unit/specs`目录下，每个测试文件以`*spec.js`文件名结尾。创建`array.spec.js`文件测试`Array`

```
describe('Array', ()=>{
	//测试序列
})
```

**序列的嵌套**

```
describe('User', ()=>{
	describe('Address', ()=> {
		//...
	})
})
```

**测试用例**

测试序列内至少要有一个`it`块，否则`Mocha`会忽略测试序列内的内容，不执行任何动作

```
describe('Array', ()=>{
	it('应该在初始化后长度为0', ()=> {
		//测试代码的实现
	})
})
```

**断言**

`Chai`断言库有两种语法，一种是`expect`，一种是`should`

```
describe('Array', ()=>{
	it('应该在初始化后长度为0', ()=> {
		var arr = []
		expect(arr).to.be.lengthOf(0)
	})
})
```

以下关键字仅是为了增加可读性，实际并无任何效果

* to
* be
* been
* is
* that
* and
* have
* with
* at
* of
* same
* a
* an

**钩子方法**

* beforeEach
* afterEach
* before
* after

```
describe('Array', ()=>{
	var expectTarget = []
	
	beforeEach(() => {
		expectTarget.push(1)
	})
	
	afterEach(() => {
		expectTarget = []
	})
})

describe('UkButton', ()=>{
	var vm = undefined
	
	before(() => {
		vm = new UkButton({propsData: {
			color: 'primary'
		}}).$mount()
	})
	
	after(() => {
		vm.destroy()
	})
})
```

**异步测试**

在`it`内加入一个`done`函数，在所有的断言执行完成后调用`done()`就可以释放测试用例并告知`Mocha`测试的执行结果

```
describe('User', ()=>{
	describe('#save()', ()=>{
		it('应该成功保存到服务器且不会返回任何错误信息', done=>{
			const user = new User('Luna')
			user.save(done)
			//或者
			//user.save(err => {
			//	if (err) done(err)
			//	else done()
			//})
		)
	)
```

**待定测试**

只要不传入`it`的第二个参数，就是默认测试是待定的

```
describe('Array', ()=>{
	describe("#indexOf()", ()=>{
		it('should return -1 when the value is not present')
	})
})
```

**重试**

```
describe('重试'， ()=>{
	//指定最大的重试次数为4
	this.retries(4)
	
	beforeEach(()=>{
		browser.get("http://www.yahoo.com")
	})
	
	it('应该在第三次重试后成功', ()=>{
		this.retries(2)
		expect($('.foo').isDesplayed()).to.eventually.be.true
	})
})
```

## 组件的单元测试方法

```
describe('my-component', ()=>{
	it('$mount()', ()=>{
		const vm = new MyComponent({
			propsData: { //注意是propsData，不是props
				msg: expectedMsg
			}
		})
		expect(vm.$el.textContent).to.be.eq(expectedMsg)
	})
})
```

摘自模板的组件测试代码

```
describe('Hello.vue', () => {
  it('should render correct contents', () => {
    const Constructor = Vue.extend(Hello)
    const vm = new Constructor().$mount()
    expect(vm.$el.querySelector('.hello h1').textContent)
      .to.equal('Welcome to Your Vue.js App')
  })
})
```

## 单元测试中的仿真技术
`Sinon`将测试替身分为3种类型

* Spies - 可以模拟一个函数的实现，检测函数调用的信息
* Stubs - 与Spies类似，但是会完全替换目标函数，这使得一个被stubbed的函数可以执行任何你想要的操作
* Mocks - 通过组合Spies和Stubs，使替换一个完整对象更容易

另外还提供了辅助方法

* Fake times - 可以用来穿越时间，例如触发一个setTimeout
* Fake XMLHttpRequest and server - 用来伪造AJAX请求和响应

**Spies**

主要用来收集函数调用的信息，也可以用来验证某个函数是否被调用过

```
let user = {
	setName(name) {
		this.name = name
	}
}

//用setNameSpy替换掉原有的setName方法
const setNameSpy = sinon.spy(user, 'setName')

//现在开始每次调用这个方法，相关的信息都被记录下来
user.setName('Darth Vader')

//通过spy对象可以查看这些记录信息
console.log(setNameSpy.callCount)

//移除spy，重要，勿忘
setNameSpy.restore()
```

**Sinon的断言扩展**

```
//检测是否被调用过
const myFunction = (condition, callback) => {
	if (condition) {
		callback()
	}
}
describe('myFunction', ()=>{
	it('应该调用回调函数', ()=>{
		const callback = sinon.spy()
		myFunction(true, callback)
		expect(callback).to.have.been.calledOnce()
		//或者用Chai的should语法
		callback.should.have.been.calledOnce()
	})
})
```

其他还有

* sinon.assert.calledWith - 是否传入特定参数
* sinon.assert.callOrder - 是否按一定顺序被调用

如果使用`sinon-chai`插件，还可以有如下

* spy.should.have.been.called
* spy.should.have.callCount(n)
* spy.should.have.been.calledOnce
* spy.should.have.been.calledTwice
* spy.should.have.been.calledThrice
* spy.should.have.been.calledBefore(spy2)
* spy.should.have.been.calledAfter(spy2)
* spy.should.have.been.calledWithNew
* spy.should.always.have.been.calledWithNew
* spy.should.have.been.calledWith(...args)
* spy.should.always.have.been.calledWith(...args)
* spy.should.have.been.calledWithExactly(...args)
* spy.should.always.have.been.calledWithExactly(...args)
* spy.should.have.been.calledWithMatch(...args)
* spy.should.always.have.been.calledWithMatch(...args)
* spy.should.have.returned(returnVal)
* spy.should.have.always.returned(returnVal)
* spy.should.have.thrown(errorObjOrErrorTypeStringOrNothing)
* spy.should.have.always.thrown(errorObjOrErrorTypeStringOrNothing)

**Stub**

适用如下场景

* 替换掉那些使测试变慢或是难以测试的外部调用
* 根据函数返回值来触发不同的代码执行路径
* 测试异常情况

```
export default {
	methods: {
		saveUser(user) {
			this.$http.post('/users', {
				first: user.firstname,
				last: user.lastname
			})
		}
	}
}			
```

用`stub`就能使测试变得简单

```
describe('saveUser', ()=>{
	it('应该在保存成功后进行回调', ()=>{
		//stub $.post，这样不用真正发送请求
		const post = sinon.stub($, 'post')
		post.yields()
		
		//针对回调函数使用一个spy
		const callback = sinon.spy()
		
		saveUser({
			firstname: 'Han',
			lastname: 'Solo'
		}, callback)
		
		post.restore()
		
		expect(callback).to.have.been.calledOnce
	})
})
```

Stub另一个常见场景是验证某个函数被调用时是否传入正确的参数

```
describe('saveUser', ()=>{
	it('应该向指定URL发送正确的参数', ()=>{
		//设置stub
		const post = sinon.stub(vm.$http, 'post')
		
		const expectedUrl = "/users"
		const expectedParams = {
			first: 'Expected first name'
			last: 'Expected last name'
		}
		
		//创建将要作为参数的数据
		const user = {
			firstname: expectedParams.first
			lastname: expectedParams.last
		}
		
		saveUser(user, ()=>{ })
		post.restore()
		
		sinon.assert.calledWith(post, expectedUrl, expectedParams)
	})
})
```

**接口仿真**

测试向localStorage写入数据的`store.js`

```
describe("incrementStoredData", ()=>{
	it('应该将存储值递增1', ()=>{
		const storeMock = sinon.mock(store)
		storeMock.expects('get').withArgs('data').returns(0)
		storeMock.expects('set').once().withArgs('data', 1)
		
		incrementStoredData()
		
		storeMock.restore()
		storeMock.verify()
	})
})
```

一个最佳实践是把测试代码包裹进`sinon.test()`中，这样利用了`sinon`的沙盒特性，允许我们通过`this.spy()` `this.stub()` `this.mock()`来创建`spy` `stub` `mock`。创建的测试替身都将被自动清理，不需要`restore()操作`

```
it('test', sinon.test(()=>{
		const stub = this.stub(vm.$http, 'post')
		
		doSomething()
		
		sinon.assert.calledOnce(stub)
	})
)
```

**Fake**

前置Faker，不会产生`XMLHttpRequest`

```
describe("Home", ()=>{
	before() {
		this.xhr = sinon.useFakeXMLHttpRequest()
		const requests = this.requests = []
		this.xhr.onCreate = xhr=>{
			requests.push(xhr)
		}
	}
	
	after(){
		this.xhr.restore()
	}
	it("应该从服务器获取图书数据", ()=>{
		const callback = sinon.spy()
		expect(this.requests).to.lengthOf(1)
		this.requests[0].respond(200, {"Content-Type": "application/json"}, '[{"id": 12, "title": "Vue2"}]')
		expect(callback).to.be.calledWith([{id: 12, title: "Vue2"}])
	})
})
```

后置Faker，会产生`XMLHttpRequest`，但被拦截

```
describe("Comments", ()=>{
	before() {
		this.server = sinon.fakeServer.create()
	}
	
	after(){
		this.server.restore()
	}
	it("应该从服务器获取评论", ()=>{
		this.server.respondWith("GET", "/some/article/comments.json", [200, {"Content-Type": "application/json"}, '[{"id": 12, "comment": "Hey there"}]'])
		
		const callback = sinon.spy()
		myLib.getCommentsFor("/some/article/", callback)
		this.server.respond()
		
		sinon.assert.calledWith(callback, [{id: 12, comment: "Hey there"}])
	})
})
```

## 调试
* 安装`Vue-DevTools`
* 使用debugger关键字

## Nightwatch入门

端到端测试侧重于检测界面的交互效果与操作逻辑是否正确

```
describe("Test TODO UI", () => {
	it('Should be this TODO UI', (client, done) => {
		client.url('http://localhost:8080')
			.waitForElementVisible('body', 1000)
			.assert.elementPresent('input[type="text"]')
			.getAttribute('input[type="text"]', 'placeholder', result => {
				this.assert.equal(result.value, 'write your todo')
			})
			.end()
	})
})
```

如果使用`XPath`来选择元素

```
this.todoDemoText = browser => {
	browser
		.userXpath()
		.click("//tr[@data-search]/span[text()='Search Text']")
		.useCss()
		.setValue('input[type=text]', 'Vue')
}
```

使用BDD式断言

```
describe('图书视图', () => {
	it('快速搜索', client => {
		client.url('http://localhost:8080')
			.pause(1000)
			
		client.expect.element('body').to.be.present.before(1000)
		
		client.expect.element('#app').to.have.css('display')
		
		client.expect.element('body').to.have.attribue('class').which.contains('示例')
		
		client.expect.element('#searchbox').to.be.an('input')
		
		client.expect.element('#seachbox').to.be.visible
		
		client.end()
	})
})
```

`Nightwatch`完全兼容`Mocha`语法的`before/after`和`beforeEach/afterEach`钩子函数，每个函数会传入一个浏览器实现的参数

```
describe('test', () => {
	before(browser => {
	
	})
	
	after(browser => {
	
	})
	...
})
```

异步操作一定要调用`done`通知

```
describe('test', () => {
	beforeEach(browser => {
		setTimeOut( () => {
			//do async task
			if(error){
				done(error)
			}
			done()
		}, 1000)
	})
	...
})
```

可以在外部全局变量中定义一个`asyncHookTimeout`属性来增加超时量