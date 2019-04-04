# webpack 4.0

## 一、初识webpack

### 1.webpack 它究竟是什么？

> 首先做一个简单的程序编写

` index.html`

``` html
<html>
    <head>
        <title>webpack学习</title>
    </head>
    <body>
        <div id="app"></div>
        <script src="./index.js"></script>
    </body>
</html>
```

` index.js`

``` js
let app = document.getElementById('app')

let header = document.createElement('h1')
header.innerText = '我是头部'
app.append(header)

let mainer = document.createElement('h1')
mainer.innerText = '我是主体'
app.append(mainer)

let footer = document.createElement('h1')
footer.innerText = '我是底部'
app.append(footer)

```

示例如下：

![示例](./images/2-1-01.jpg)



这时，我们就可以插入到了内部了。但是，随着内容的增大，代码的复用，代码的可读性等方面来考虑一个项目，所以，我们不得不用面向对象的方法来编程，或者分包的方式来编写程序。

如：

` header.js`

``` js
function Header() {
    let header = document.createElement('h1')
    header.innerText = '我是头部'
    app.append(header)
}
```

` mainer.js`

``` js
function Mainer() {
    let mainer = document.createElement('h1')
    mainer.innerText = '我是主体'
    app.append(mainer)
}
```

` footer.js`

```js
function Footer() {
    let footer = document.createElement('h1')
    footer.innerText = '我是底部'
    app.append(footer)
}
```

` index.js`

```js
let app = document.getElementById('app')
new Header()
new Mainer()
new Footer()
```

` index.html`

```html
<html>
    <head>
        <title>webpack学习</title>
    </head>
    <body>
        <div id="app"></div>
        <script src="./header.js"></script>
        <script src="./mainer.js"></script>
        <script src="./footer.js"></script>
        <script src="./index.js"></script>
    </body>
</html>
```



示例如下：

![示例](./images/2-1-02.jpg)



> 上述方式同样能达到效果，并拆分开来了。变得更好对每个js文件维护了。但有什么不好影响呢？

1. 每一个js文件都需要请求获取。
2. 途中哪个js文件有问题，难以定位(若把 index.js 和 footer.js 换位置会报 `Footer is not function `错误)

所以，我们要想个办法把 js 合并一下，并让结构更清晰。所以，我们先初用 为 `webpack `试试



首先，要下载 Node，没有Node的同学请[下载Node](https://nodejs.org/zh-cn/)，Node 会自带 npm 下包工具

然后，到一个自己的项目目录下打开 cmd，安装webpack

`npm install webpack webpack-cli -D`



> <u>疑点 ：webpack-cli 是什么？</u>（之后解答）



代码如下：

` header.js`

```js
function Header() {
    let header = document.createElement('h1')
    header.innerText = '我是头部'
    app.append(header)
}
export default Header
```

` mainer.js`

```js
function Mainer() {
    let mainer = document.createElement('h1')
    mainer.innerText = '我是主体'
    app.append(mainer)
}

export default Mainer
```

` footer.js`

```js
function Footer() {
    let footer = document.createElement('h1')
    footer.innerText = '我是底部'
    app.append(footer)
}
export default Footer
```

` index.js`

```js
import Header from './header.js'
import Mainer from './mainer.js'
import Footer from './footer.js'

let app = document.getElementById('app')
new Header()
new Mainer()
new Footer()
```

在命令行执行：`npx webpack index.js`

index.html`

```html
<html>
    <head>
        <title>webpack学习</title>
    </head>
    <body>
        <div id="app"></div>
        <script src="./dist/main.js"></script>
    </body>
</html>
```

效果等同于之前，我们来解释一下上面的代码。

``` js
/*
	import 是用于导入文件的，见 index.js。
	
	export 是用于导出内容的，至于是字符串，还是函数，还是对象都可以。加个 default 表示没有指定要导出什么内容时，自动导出这个default 的内容0，每个js 只能有一个 default，但能有多个 export。
	
	这里我们为什么没有直接运行，而是先执行 npx webpack index.js，目录下多出一个dist 文件夹，里面多出一个main.js 的问题件？并且，我们要在 index.html 中引入 main.js 呢？
	因为，浏览器不能识别 import 和 export 是 ES6 的模块化导包方式。
	import 和 export 是 ES6 的模块化导包方式，但是 webpack 能识别，所以，webpack 充当了翻译官的角色。他把你的index.js 的依赖关系解析后生成 浏览器能识别的内容。
*/
```

> 疑问：npx 又是什么呢？ 
>
> npx 就是一个临时的执行依赖包的指令，及时的完成自动打包。[详情请看](https://www.jianshu.com/p/cee806439865)



> 提问：那，webpack 完全就是一个翻译官，把浏览器的看不懂的文件编译成可以看懂的文件？
>
> 答：根据官网的解答：Webpack is a module bundler (webpack 是一个模块打包工具).简单来说，webpack 只能对模块内容进行打包。所以，他自身只能识别上述案例中的 import 和 export 。除了 ES Module 以外，它还能打包 CommonJS，CMD，AMD，会的同学不妨尝试修改一下，同样是能成功。
>
> 除此之外，我们发现在一些地方(如，脚手架)，webpack 还可以打包 css，font，image等等，我们只要安装相应的 plugin(插件) 和 loader(加载器) ，我们就可以把想打包的内容合并成一个或者几个文件。
>
> 所以，准确的理解就是，webpack 就是一个为把资源打包而生的工具。



