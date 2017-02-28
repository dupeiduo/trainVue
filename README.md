# trainVue
#SPA 与前后端分离（密不可分）
##SAP
> 在使用期间不会重新加载页面，页面局部刷新，用户体验好；路由、业务逻辑前端控制。

*缺点：* 初次加载耗时多，SEO难度较高

##前后端分离
> 前端负责界面显示，后端负责吞吐数据和计算，业务逻辑前端实现，减轻服务器压力。修改需求时大多不影响后端。

- 页面渲染不依赖服务器
- 技术栈互不影响
- 接口全部使用 JSON 或 XML 的统一数据格式通信
- 组件化、工程化不依赖后端
- 便于引入中间层
- 便于将来添加更多终端

#[MV*](http://www.ruanyifeng.com/blog/2015/02/mvcmvp_mvvm.html)
##MVC
*2010年诞生的backbone 提出的前端mvc*

> 1. 用户可以向 View 发送指令（DOM 事件），再由 View 直接要求 Model 改变状态。
2. 用户也可以直接向 Controller 发送指令（改变 URL 触发 hashChange 事件），再由 Controller 发送给 View。
3. Controller 非常薄，只起到路由的作用，而 View 非常厚，业务逻辑都部署在 View。

##[MVP（类似改版前的前端版本）](http://www.baike.com/wiki/MVP%E6%A8%A1%E5%BC%8F)

> - 各部分之间的通信，都是双向的。
- View 与 Model 不发生联系，都通过 Presenter 传递。
- View 非常薄，不部署任何业务逻辑，称为"被动视图"（Passive View），即没有任何主动性，而 Presenter非常厚，所有逻辑都部署在那里。

*P(Presenter: 相当于controller)*

##MVVM

> MVP的升级，框架将我们之前自己写的 P 部分封装成 VM 。做了双向绑定，我们只需要关注  M（数据）和 V（视图）即可。不用自己做重渲染

- View的变动，自动反映在 ViewModel 
- Model 的改动也会自动反映在 ViewModel

#技术栈架构

> 1. 环境依赖（nodejs">= 4.0.0", npm">= 3.0.0"）
2. 前端框架（Vue "2.1.0"）：详见下节
3. [脚手架（vue-cli）](https://github.com/vuejs/vue-cli)：简化手动配置安装过程
4. [打包工具（webpack "1.13.2"）](http://webpack.github.io/docs/configuration.html)：提高工作效率，配置更透明，插件使用、管理更方便统一
5. 高级语言（Less、 ES6）：代码抽象程度高，更优美易读 ->更易于维护
6. 安装

安装 nodejs & npm

	npm install -g vue-cli
	vue init webpack <my-project-name>
	npm install
	npm run dev

*项目结构？*

#[Vue](https://cn.vuejs.org/v2/guide/)

##组件化框架的作用
> 前端的组件化框架，本质上就是借助virtual Dom帮助开发者管理混乱的Dom，并提供给开发者像操作状态机一样操作页面的能力。（代码和文件结构更清晰易于维护）

##Vue 是什么？能做什么？

> Vue 是典型MVVM框架，双向绑定与完整的组件化方案，利用 virtual Dom 极大提高了性能。

*你不必做任何事就获得完全优化的重渲染。*

##原理
> - 订阅发布模式
> - 响应式数据流

###订阅发布模式之解读 Object.defineProperty
> 订阅发布是一个非常常见的设计模式，原理也非常简单就是订阅者订阅信息，然后发布者发布信息通知订阅者更新。
> 
> 在对象定义的过程中，通过get 得到订阅者的依赖列表，通过set 发布消息从而订阅者获得消息
	
	var data = {}		//可理解为组件中的data
	data.name = "Peiduo"	//组件中的一个属性
	var myValue = data.name + " Du";	//更新该属性的
	var key = "name"
	Object.defineProperty(data, key, {
    	get:function (){
	        setDepsList()	//设置 V 和 M 中使用该属性的地方（订阅者订阅）
	        return myValue
    	},
    	set:function (value){
	        tellDepsListToUpdate()    // 告诉要更新的 V 和 M （发布者发布）
	        myValue = value
    	}
	});

##常用API解读与一些最佳实践
> - Vue 生命周期（keep-alive）
- 变量绑定
- 组件定义的方式
- 父子组件通信机制（props参数传递的方式，事件绑定的方式）
- slot 分发内容
- 如何使用组件内的方法
- 覆盖elementUI 样式需要注意
- 内存缓存机制




#[webpack](http://webpack.github.io/docs/)
*webpack is a module bundler.--模块打包机*

##webpack 能做
> - Split the dependency tree into chunks loaded on demand（把依赖树分割成块按需加载）
- Keep initial loading time low
- Every static asset should be able to be a module 
- Ability to integrate 3rd-party libraries as modules （整合CommonsChunkPlugin）
- Ability to customize nearly every part of the module bundler （定制）
- Suited for big projects 

##基本组成  
		
	module.exports = {
     	entry: './src/app.js',
    	output: {
         	path: './bin',
         	filename: 'app.bundle.js'
     	}
     }
      //没有特殊要求、特殊文件,以上代码可以完成模块打包的功能

##resolve(模块文件、路径相关配置)

##Loaders(加载器)

*Loaders are special modules webpack uses to ‘load’ other modules (written in another language) into JavaScript (that webpack understands).*

Loaders把其它语言转换为它认识的javascript

##Plugins(插件)
*To do some additional processing of the bundle in your workflow.*

额外的包整合，压缩、混淆等...

#项目架构
> - 入口（main.js -> index.html -> router -> app.vue -> router-> pages）
- 配置（2处）
- vuex（主要用于登录）
- 路由（定义、参数的使用、生命周期钩子的使用）
- 请求层（基础请求、缓存层）
- 模型层（不改变接口数据，反回符合业务需求的数据模型）
- 项目间通用组件（详见下文）
- 项目内通用组件（项目内部个性化的可重用组件）
- 页面（组织结构，页面内组件组织结构）
- 公共方法、工具类的提取（无依赖，可复用）
- 静态资源


#项目间通用组件 [npm link](https://docs.npmjs.com/cli/link) 

> - 原理，文件的软连接
- 管理方式 svn + gulp （ES6 -> ES5）
- 使用:更新svn 进入项目目录执行如下命令即可（nodejs 脚本）

node initlink.js <大于等于三个参数［通用组件所在路径, 项目跟路径, component^version component1^version］>

	node initlink.js /Users/dupeiduo/Desktop/Program/VueComponents/tags 
	/Users/dupeiduo/Desktop/Program/OSS/truck/vue-webpack 
	agrisz-map^1.0.0 agrisz-echart^1.0.0 agrisz-dialog^1.0.0 
	agrisz-button^1.0.0 agrisz-searchpoi^1.0.0
#


#讨论与补充
