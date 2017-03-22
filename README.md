
## Vue 使用总结和交流




[toc]
## SPA 与前后端分离（密不可分）


### SAP

> 在使用期间不会重新加载页面，页面局部刷新，用户体验好；路由、业务逻辑前端控制。

*缺点：* 初次加载耗时多，SEO（Search Engine Optimization）难度较高（Prerender.io）


### 前后端分离

> 前端负责界面显示，后端负责吞吐数据和计算，业务逻辑前端实现，减轻服务器压力。修改需求时大多不影响后端。

- 页面渲染不依赖服务器
- 技术栈互不影响
- 接口全部使用 JSON 或 XML 的统一数据格式通信
- 组件化、工程化不依赖后端
- 便于引入中间层
- 便于将来添加更多终端


## [MV*](http://www.ruanyifeng.com/blog/2015/02/mvcmvp_mvvm.html)

### MVC

*2010年诞生的backbone 提出的前端mvc*

> 1. 用户可以向 View 发送指令（DOM 事件），再由 View 直接要求 Model 改变状态。
> 2. 用户也可以直接向 Controller 发送指令（改变 URL 触发 hashChange 事件），再由 Controller 发送给 View。
> 3. Controller 非常薄，只起到路由的作用，而 View 非常厚，业务逻辑都部署在 View。


### [MVP（类似改版前的前端版本）](http://www.baike.com/wiki/MVP%E6%A8%A1%E5%BC%8F)


> - 各部分之间的通信，都是双向的。
> - View 与 Model 不发生联系，都通过 Presenter 传递。
> - View 非常薄，不部署任何业务逻辑，称为"被动视图"（Passive View），即没有任何主动性，而 Presenter非常厚，所有逻辑都部署在那里。

*P(Presenter: 相当于controller)*


### MVVM


> MVP的升级，框架将我们之前自己写的 P 部分封装成 VM 。做了双向绑定，我们只需要关注  M（数据）和 V（视图）即可。不用自己做重渲染

- View的变动，自动反映在 ViewModel 
- Model 的改动也会自动反映在 ViewModel


## 技术栈架构


> 1. 环境依赖（nodejs">= 4.0.0", npm">= 3.0.0"）
> 2. 前端框架（Vue "2.1.0"）：详见下节
> 3. [脚手架（vue-cli "2.5.1"）](https://github.com/vuejs/vue-cli)：简化手动配置安装过程
> 4. [打包工具（webpack "1.13.2"）](http://webpack.github.io/docs/configuration.html)：提高工作效率，配置更透明，插件使用、管理更方便统一
> 5. 高级语言（Less、 ES6）：代码抽象程度高，更优美易读，更易于维护
> 6. 安装(配置国内代理 **npm config set registry http://registry.cnpmjs.org**)

安装 nodejs & npm
```bash
  npm config set registry http://registry.cnpmjs.org
  npm install -g vue-cli
  vue init webpack <my-project-name>
  npm install
  npm run dev
```


## [Vue](https://cn.vuejs.org/v2/guide/)



### 组件化框架的作用

> **前端的组件化框架，本质上就是借助virtual Dom帮助开发者管理混乱的Dom，并提供给开发者像操作状态机一样操作页面的能力。**（代码和文件结构更清晰易于维护，个人感觉这个概念的灵感来自工业设计）

*不会搞艺术的程序员不是好设计师         ---vue 作者 尤雨溪*


### Vue 是什么？能做什么？


> Vue 是典型MVVM框架，拥有双向绑定的能力与完整的组件化方案，利用 virtual Dom 提供了函数式的 UI 编程方式，*可以后端渲染*。

**你不必做任何事就获得完全优化的重渲染。**


### 原理

> - 订阅发布模式
> - 响应式数据流


#### 订阅发布模式之解读 Object.defineProperty（类似每个对象的构造函数）

> - 订阅发布模式：订阅者订阅信息，然后发布者发布信息通知订阅者更新。
> - 在对象定义的过程中，通过get 得到订阅者的依赖列表，通过set 发布消息从而订阅者获得更新通知
  
```javascript
  var data = {}   //可理解为组件中的data
  var name = "Peiduo" //组件中的一个属性
  var key = "name"
  
  Object.defineProperty(data, key, {
      get:function (){
          setDepsList()  
          //订阅者订阅（设置 V 和 M 中使用该属性的地方）
          return name
      },
      set:function (value){
          tellDepsListToUpdate(value)  
          //发布者发布（告知要更新的V 和M ）
          name = value
      }
  });
  
  data.name += " Du"; //更新该属性

```
### 常用API解读与一些最佳实践


#### Vue 生命周期（keep-alive 保留组件状态或避免重新渲染，将页面缓存到内存中）

每次初始化页面会依次执行以下钩子函数
> beforeCreate
> created
> beforeMount
> mounted
> activated
> beforeUpdate
> updated

如果使用keep-alive标签，在离开页面时不销毁页面，会执行
> deactivated

再次回到该页面会执行
> activated
> beforeUpdate
> updated

不使用keep-alive，离开时执行
> beforeDestroy
> destroyed

当再次回到该页面将会再次全部执行一遍钩子函数


#### 变量绑定（容易产生的误区）

在标签中绑定变量时，如果传输字符串（比如属性名为 dataName）
```javascript
dataName="dpd"  // dpd 就是一个字符串，不是变量
```
区别于字符串的其它对象类型
```javascript
:dataBool="true"  // 带冒号则双引号内表示为表达式
:dataNumber="10"
:dataArray="arrList" // arrList 为自定义数组（来自data 或者 props 中）
```


#### 组件定义的方式

1. 构成组件，项目中应用最多的 (props down,  events up)
2. 全局组件，应用：通用组件
3. 在特定结构标签内使用组件

```vbscript-html
<table>
  <tr is="my-row"></tr>
</table>
```


#### 父子组件通信机制

父组件通知子组件： props参数传递的方式
子组件通知父组件：事件绑定的方式


#### 非父子组件通信

1. 有共同父组件的，通过共同的父组件控制
2. vuex
3. 全局事件机制
```javascript
window.vueBus = new Vue()

// 触发组件 A 中的事件
vueBus.$emit('id-selected', 1)

// 在组件 B 创建的钩子中监听事件
vueBus.$on('id-selected', function (id) {
  // ...
})

```
#### slot 分发内容



#### 如何使用子组件内的方法

> 当我们想要在父组件使用子组件内的方法时可以通过 子组件索引ref 实现

*通常情况不需要使用子组件内的方法，通过改变其属性即可，避免在模版或计算属性中使用*


#### 覆盖elementUI 样式需要注意添加命名空间



#### v-else 内部使用循环的时候，v-else 有逻辑上的问题，for 优先级比较高


    for (item in list) {
    else {
      // create <tr> element.
    }
  }

1. 可以使用如下方式替换
  
  v-if="useA"
  v-if="!useA"

2. 外部用 template 包裹


## [webpack1.0 API](http://webpack.github.io/docs/)  

[webpack2.0 概念](https://webpack.js.org/concepts/)
[中文](http://www.css88.com/doc/webpack2/concepts/entry-points/)
*webpack is a module bundler.*--模块打包机*


### webpack 能做

> - Split the dependency tree into chunks loaded on demand（把依赖树分割成块按需加载）
> - Keep initial loading time low
> - Every static asset should be able to be a module 
> - Ability to integrate 3rd-party libraries as modules （整合）
> - Ability to customize nearly every part of the module bundler （定制）
> - Suited for big projects 


### 基本组成  


```javascript
  module.exports = {
      entry: './src/app.js',
      output: {
          path: './bin',
          filename: 'app.bundle.js'
      }
     }
      //没有特殊要求、特殊文件,以上代码可以完成模块打包的功能

```
### resolve

路径、文件相关配置


### Loaders(转换器)


*Loaders are special modules webpack uses to ‘load’ other modules (written in another language) into JavaScript (that webpack understands).*

**Loaders in webpack transform these files into modules as they are added to your dependency graph.**

webpack 只认识javascript ，它把其他文件看作是模块，加到依赖树中


### Plugins(插件)

*To do some additional processing of the bundle in your workflow.*

额外的包整合，压缩、混淆等...


## package

[权威：包的作用与使用方式](https://www.npmjs.com/package/package)


### package 中版本前的符号~和^

```bash
  # npm@2.6.1 or later

  npm update <package name>
  
  
  npm update # 更新所有package 到tags 中最新版本,包括全局包、本地包、依赖

  "dependencies": {
    "dep1": "^1.1.1"  #可更新小于最大版本号的版本
  }
  
  "dependencies": {
    "dep1": "~1.1.1"  #可更新最小版本号
  }
  
  npm update --save # 更新 dependencies中包，package.json 会被修改（只写了这里）

  
  npm update -g # 更新全局包

```



## 项目架构

- 入口（main.js -> index.html -> router -> app.vue -> router-> pages）
- 配置（2处）
- vuex（主要用于登录）
- 路由（定义、参数的使用、生命周期钩子的使用）
- 请求层（基础请求、缓存层详见下文）
- 模型层（不改变接口数据，反回符合业务需求的数据模型）
- 项目间通用组件（详见下文）
- 项目内通用组件（项目内部个性化的可重用组件）
- 页面（组织结构，页面内组件组织结构）
- 公共方法、工具类的提取（无依赖，可复用）
- 静态资源



## 项目间通用组件 [npm link](https://docs.npmjs.com/cli/link) 


> - 原理，文件的软连接
> - 管理方式 svn + gulp （ES6 -> ES5）
> - 使用:更新svn 进入项目目录执行如下命令即可（nodejs 脚本）

  node initlink.js <componentPath> <programPath> <component^version> <component1^version>

```bash
  node initlink.js /Users/dupeiduo/Desktop/Program/VueComponents/tags 
  /Users/dupeiduo/Desktop/Program/OSS/truck/vue-webpack 
  agrisz-map^1.0.0 agrisz-echart^1.0.0 agrisz-dialog^1.0.0 agrisz-button^1.0.0 agrisz-searchpoi^1.0.0
```


## 设计与思考

#### 内存缓存机制（项目实例）


我们可以也有必要在任何统一入口处做一些事情
#### 以数据为导向

1. 做一个页面先想清楚它所需要的**数据** （立马开始动手是**大忌**）
2. 理清宏观的整体页面结构 

> *页面就像个机器，它的“变量”就是控制机器运行的操纵器，先清楚要有哪些操纵器*


#### 代码要有顺序，有逻辑关系（努力方向）

按照加载的顺序、操作的顺序（人们容易理解的顺序）
变量名方法名自上而下有逻辑关系


#### [自顶向下](http://baike.baidu.com/link?url=idtDSUDzLuXUuKewE3L0nmZo-cBEabf5wtKWV3ZAtlsA_axKhDRf079J
EUKdbd5IN0fKJ6DPCK3IuiV8-IVDHEzv69G9-pqNIiNl64QJYyglNNd9Hmf19ACgO-6X7dUd) （分析问题的一种思维方式）
**思想就是分解问题，问题分解后，单看每个小问题，就非常简单了（不够简单那是分的不够细）。**

> 反映到代码中每个小问题就是一个**方法**，每个**方法**只做一件事。当分的足够细的时候，代码抽象也就做好了


##### 应用

1. 工作流
- 整理页面所需数据
- 搭建整体页面结构（父组件结构，由多个子组件组成）
- 设计子组件内部数据属性，回调事件
- 模拟页面操作逻辑，mock数据
- 完善方法和样式，调试接口

**把有困难的或者可以分工的部分分配出去，从而达到最大限度分工协作**

2. 搭建项目架构可以用相同的思想


#### 建议

> 对自己的代码有强烈的控制欲
> 对自己写的代码有信心 
> 对性能有极致需求的特殊情况，其实应该牺牲一些可维护性采取手动优化


#### 了解

[WebAssembly](http://www.ithome.com.tw/news/96924)
[Github](https://github.com/WebAssembly/design)


## 欢迎补充
