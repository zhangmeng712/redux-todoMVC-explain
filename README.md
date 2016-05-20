# Awesome Redux

## 内容

- Why Redux（Flux）
- Awesome Redux todoMVC example
	- awesome webpack 
	- awesome react & es6
	- awesome redux
 	

## Why Flux（Redux）

### 思考这两种场景

 1）现有的MVC框架中，C(Contoller)层都是被弱化的，很多的逻辑都被书写在View层
 当多个View同时修改同一份Model的时候，数据往往是“不可预测的”，在MV VM的双向绑定的设计中，这个问题更严重。
 
 2）使用类似React框架 由组件“堆积“起来的页面，没有专门统筹的机制管理组件通信，所以需要在组件内部，处理除UI以外的业务逻辑，造成组件无法复用。
 
![1](https://cdn.infoq.com/statics_s2_20160517-0032u2/resource/news/2014/05/facebook-mvc-flux/en/resources/flux-react-mvc.png)

![2](http://andrewhfarmer.com/img/2015/11/any-to-any.png)
 
 
###Flux

Flux 的核心就是一个简单的约定：视图层组件不允许直接修改应用状态，只能触发 action。应用的状态必须独立出来放到 store 里面统一管理，通过侦听 action 来执行具体的状态操作。  - by vue.js作者

<img src="http://staltz.com/img/flux-unidir-ui-arch.jpg" width="500px" height="300px">


### Redux

####Redux三大概念

- action：决定组件去做哪种操作，例如 添加一条数据 
- store：state数据的集合（包括请求返回的数据以及）state决定UI展现
- reducer：真正操作返回新state给store，经由store传递给组件更新

<img src="http://image.slidesharecdn.com/using-redux-160119165259/95/using-redux-7-638.jpg?cb=1453222549" width="500px" height="360px">



- Redux不是一个框架，是Flux的一种实现方式，实现数据源的“可预测”
- Redux可以配合插件用在各个框架上，例如React-redux
- Redux使用Reducer不是原地修改数据，而是返回一份全新的数据。这个设计巧妙的地方在于不依赖任何库就模拟了 immutable data
- 提供丰富的middleware例如 redux-thunk
 
 ![了解数据状态](https://img.alicdn.com/tps/TB13MDeJVXXXXbyXVXXXXXXXXXX-390-504.jpg)
 


## Awesome Redux todoMVC example

### awesome webpack

#### 编译react和ES6

```javascript
//package.json配置
{
  "name": "redux-todoMVC-app",
  "version": "0.0.1",
  "scripts": {
    "start": "webpack -w",
    "build": "webpack -p"
  },
  "devDependencies": {
    "babel-core": "^6.8.0",
    "babel-loader": "^6.2.4",
    "babel-preset-es2015": "^6.6.0",
    "babel-preset-react": "^6.5.0",
    "webpack": "^1.13.0"
  },
  "dependencies": {
    "react": "^0.14.7",
    "react-dom": "^0.14.8",
    "classnames": "^2.2.5",
    "react-redux": "^4.4.5",
    "redux": "^3.5.2",
    "redux-thunk": "^2.1.0",
    "isomorphic-fetch": "^2.2.1",
    "todomvc-app-css": "^2.0.6"
  }
}

```

```javascript
// webpack.config.js
var path = require("path");
module.exports = {
    entry: path.resolve(__dirname, 'js/app.js'),
    output: {
        path: path.join(__dirname, 'out'),
        filename: 'bundle.js'
    },
    module: {
        loaders: [
            //处理react模板的配置
            {           
                test: /\.js$/,
                exclude: /(node_modules|bower_components)/,
                loader: "babel-loader",
                query: {
                    presets: ['react', 'es2015']
                }
            }
        ]
    },
    plugins: []
};
```

#### 使用webpack进行无线端的开发实例

- 1 合并依赖的js生成一个js文件并压缩
- 2 合并依赖的css成一个css文件并压缩
- 3 html-loader获取模板使用 crox mustache等模板引擎渲染
- 4 url-loader可以定义规则例如小于10k的图片自动转为base64
- 5 使用commonjs的开发模式开发页面,自动转化成browser端运行的程序，不用考虑全局变量
- 6 “开发友好”:可以根据需要对页面进行 组件 区块的拆分（提高复用度），降低维护的门槛
- 7 loader比require简单实用

<img src="https://img.alicdn.com/tps/TB1GDbmJVXXXXbdXFXXXXXXXXXX-1327-674.jpg" width="800px" height="450px">

```javascript
//package.json
{
  "name": "etao-m-super-list",
  "version": "0.0.1",
  "scripts": {
    "start": "webpack -w",
    "build": "webpack -p"
  },
  "devDependencies": {
    "babel-core": "^6.8.0",
    "babel-loader": "^6.2.4",
    "css-loader": "^0.23.1",
    "extract-text-webpack-plugin": "^1.0.1",
    "html-loader": "^0.4.3",
    "style-loader": "^0.13.1",
    "url-loader": "^0.5.7",
    "webpack": "^1.13.0"
  },
  "dependencies": {
    "fastclick": "^1.0.6",
    "mustache": "^2.2.1",
    "promise": "^7.1.1"
  }
}

```

```javascript
/**
 * @fileOverView webpack.config.js
 */
var path = require("path");
var ExtractTextPlugin = require("extract-text-webpack-plugin");
module.exports = {
    entry: {
        app: "./src/app.js",
    },
    output: {
        path: path.join(__dirname, 'build'),
        filename: '[name].js'
    },
    module: {
        loaders: [
            //将依赖打包成一个css文件，文件地址out/xxx.css
            //增加import模板的功能
            //如果图片小于10kb则自动转化为base64--优化规则
            {
                test: /\.css$/,
                loader: ExtractTextPlugin.extract("style-loader", "css-loader")
            }, {
                test: /\.html$/,
                loader: 'html'
            }, {
                test: /\.(png|jpg)$/,
                loader: 'url?limit=10000'
            }
        ]
    },
    plugins: [
        new ExtractTextPlugin('[name].css')
    ]
};
```

###awesome react

TodoMVC应用分析

![](https://img.alicdn.com/tps/TB1hVjcJVXXXXcAXVXXXXXXXXXX-604-569.jpg)

- 组件开发
	- 应用拆分为组件，满足一个组件就做一件事情的 “原子性”
	- 设计props和state以及全局的store数据结构
	- 应用涉及的操作
	
- TodoApp
  - Header <strong>props</strong>: addTodo
	- TextInput state: isEditing <strong>props</strong>: onSave newTodo text placeHolder
- MainSection <strong>props</strong>:todos
	- TodoItem <strong>props</strong>: todo editTodo deleteTodo completeTodo
		- TextInput(复用)
	- Footer: <strong>props</strong>: completedCount activeCount onShow onClearCompleted filter

- 全局store为

```javascript
{
	todos:[
  		{
    		text: 'Use Redux',
   			completed: true,
    		id: 0
  		}
	]
}
```

- 涉及的Action操作
  - 添加一个Todo ADD_TODO
  - 删除一个Todo DELETE_TODO
  - 编辑一个Todo  EDIT_TODO
  - 修改一个todo完成状态 COMPLETE_TODO
  - 修改所有todos的状态 COMPLETE_ALL
  - 删除所有已经完成的 CLEAR_COMPLETED
  - 获取Todos列表 GET_HISTORY
  - 显示所有 show_all
  - 显示完成的todos show_completed
  - 显示未完成的todos show_active

![](https://img.alicdn.com/tps/TB1MKniJVXXXXXcaXXXXXXXXXXX-924-434.jpg)
    
### awesome redux

- redux和react-redux
	- store = new Store
	- 将store通过react-redux的<Provider>传递给应用TodoApp
	- 建立react-redux的Connect将action和store传入
		- 让connect监听store中state的变化
		- 分发action到各处	
	- 编写action建立对应的操作索引，注意操作索引为常量 需在contants中定义
	- 编写reducer去生成新的state
	- 使用中间件redux-thunk和fetch实现数据源的读取，异步的action
	
	
![](https://img.alicdn.com/tps/TB1gDvBJVXXXXc3XpXXXXXXXXXX-857-593.jpg)	
	











