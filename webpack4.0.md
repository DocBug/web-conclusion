# webpack 4.0

## 一、初识webpack

### 1. webpack 它究竟是什么？

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



### 2. webpack 的环境搭建

- `webpack`是基于nodejs 环境工作的。所以，我们需要先下载 nodejs。

[NodeJS 下载地址](https://nodejs.org/zh-cn/)

下载成功的标志：进入命令行输入 `node -v` 会出现版本号。`npm -v `  npm也会一同被安装。

- 接下来，创建一个文件夹作为项目根目录，并在文件夹中启动命令行工具。

输入 `npm init` 或者 `npm init -y`，这个指令是用来初始化包管理文件的命令，会在根目录生成一个 package.json 文件，里面描述的就是项目的相关配置信息，每个字段的[详情请看](https://www.cnblogs.com/whoismagin/p/5627308.html)

- 安装 `npm webpack webpack-cli -g` ，其实 -g 表示全局安装。

可能有安装不上，会很慢，是因为 npm 的数据获取源被屏蔽或者在国外，无法被正常下载。因此，我们可以切换我们的数据源来提升下载速度。[切换源的方式](https://blog.csdn.net/lin74love/article/details/78297808)

> P.S. 这样有一个注意事项就是，不建议 webpack 进行全局安装。因为如果有两个项目，一个是 webpack 3, 一个是 webpack 4， 这时候，如果不通过一些复杂的方式，你是启动不了 webpack 3的。如果，你既想启动 webpack 3 , 又想启动 webpack 4 ，便很难达到你想要的预期效果的。

因此，最好我们使用 `npm webpack webpack-cli -D`。

- 安装好后，在项目目录里会多一个 node_modules 文件夹，存放的就是所下载的依赖包的数据。

注：这个时候你输入 webpack -v 去测试是否下载成功，会报错。说指令不存在。因为这种方法会去全局目录查找是否有webpack 指令。既然都没安装全局，所以肯定没报 `指令不存在的错误信息`

> 那，如何去看是否安装成功呢？
> 通过指令 `npx webpack -v` 。使用 npx 会去项目中的 node_modules 中去查找是否有 webpack。



这个时候，webpack 就可以安装使用了~



### 3. webpack 的配置文件

​	之前我们用 `npx webpack index.js` 就可以打包了，但是如果不指定 index.js ，就不知道是哪个地址。

另外，我们的项目到底从哪个入口进入，从哪个出口输出，之前都是 webpack 默认的（为了开发体验更好，随着webpack 的升级，越来越多的默认配置）。

​	因此，我们要编写一个配置文件，这个配置文件配置在项目根目录选，名称为 `webpack.config.js`

`webpack.config.js`

```js
const path = require('path')	// node的内置对象，可以获取路径的相关信息

module.exports = {
    // entry: string | Array<String> | {[entryChunkName: string]: string|Array<string>}
    entry: './index.js',	// 打包的入口文件
    output:{				// 打包的出口文件
        filename: 'main.js',	// 打包后的文件名称
        path: path.resolve(__dirname, 'dist')	// 绝对路径下的dist目录内，默认也是这个地址
    }
}
```

​	这是执行 `npm webpack` 就会默认的根据此配置文件的配置进行打包

​	P.S. 需要注意的是，webpack.config.js 在根目录是默认配置项，如果自定义配置文件，可以使用

`npx webpack --config [自定义配置文件名].js`



​	到目前为止，我们可以通过配置文件打包了。但是，和我们使用的主流的脚手架使用 `npm run build` 这样的操作不相同。 那我们应该如何变得相同呢？

​	我们只需要在 package.json 的 scripts 字段中添加自己的指令即可。

​	例如：之前的配置我们采用：

`package.json`

``` json
{
    “scripts”: {
        "build": "webpack"
    }
}
```

​	这样就可以使用 `npm run build` 和我们之前的效果一样啦。等价于 执行 webpack 指令。

	> 可能有人会疑问，那这样的 webpack 不是全局的吗？
	>
	>在 scripts 中的执行会先从 node_modules 目录下去查询是否有这条指令，如果没有才会调用全局的。

## 二、loader 是什么？

### 2.1 初识loader

之前我们打包的都是 js 文件。假若，我们需要打包图片文件怎么办呢？

```js
var image = require('./1.jpg')	// 自行加入一张图片
console.log(image)
```

这个时候再执行会报错。
![报错信息](./images/3-01.jpg)

​	意思是说模块解析失败，需要使用一个 loader 来处理这种后缀的文件类型。因此，我们需要去官网去找相应的文件处理类型。

​	这里直接给出加载器名称叫 file-loader。 `npm install --save-dev file-loader`

​	安装好后，在配置文件中配置规则。

`webpack.config.js`

```js
module.exports = {
    mode: 'development',
    entry: './src/index.js',
    output: {
        filename: 'bundle.js'
    },
    module: {
        rules: [
            { test: /\.jpg$/, use: { loader: 'file-loader' } }
        ]
    }
}
```

​	然后再运行 npm run build. 就可以得到结果了。

![打包结果](./images/3-02.jpg)



​	这里只做抛砖引玉。同理，png，svg等等其他图片类型的方式，相信也很容易知道了。所有图片都是通过 file-loader 进行解析的。所以，只需要在 test 的 正则表达式式中多加几个后缀即可。

​	再发散学习一下，如果遇到 vue结尾的，其他后缀结尾的，只需要去找到相应的插件，根据上面的 rules 进行配置一个就可以配置了，是不是很简单呢？



### 2.2 loader打包静态图片配置

1. 打包后的文件被替换了名字，我们如何不改变名字以及扩展名呢？

   除了下面标注的 [name].[ext] 两个占位符，还有什么呢？[详情参照官网](https://www.webpackjs.com/loaders/file-loader/)

2. 如何指定打包后的位置？

   会在dist/images下

`webpack.config.js`

```js
module.exports = {
    mode: 'development',
    entry: './src/index.js',
    output: {
        filename: 'bundle.js'
    },
    module: {
        rules: [
            { test: /\.jpg$/,
                use: {
                    loader: 'file-loader'
                    option: {
                    	name: '[name].[ext]',	// 1.
                    	outputPath: 'images/'	// 2.
                	}
            	}
            }
        ]
    }
}
```

 3. 认识一个新朋友 url-loader

    ​	url-loader 也是可以打包图片的，那 url-loader 和 file-loader 有什么区别呢？url-loader 其实是对 file-loader 的一个加强，底层对 file-loader 进行了一次再次封装。会对图片进行 base64 的格式处理，使图片的引入到 html 中，减少 http 请求。



​	我们引入 url-loader 时，不需要引入 file-loader，因为它内部自行依赖了 file-loader, 不需要额外引入。

​	`npm i url-loader -D`

* limit 的意义：虽然说转化成 base64，可以减少请求。但是如果，图片特别大，那么 形成的 js也会变得格外的大，这样会影响页面的加载速度。因此，我们往往会对图片进行限制，如果图片的大小超过一定上限，就不进行 base64 格式转化。limit 的配置就是配置这个上限，单位是 bit。

`webpack.config.js`

```js
module.exports = {
    mode: 'development',
    entry: './src/index.js',
    output: {
        filename: 'bundle.js'
    },
    module: {
        rules: [
            { test: /\.(jpe?g|png|gif)$/,
                use: {
                    loader: 'url-loader'
                    option: {
                    	name: '[name].[ext]',
                    	outputPath: 'images/',
                    	limit: 10000			//3.
                	}
            	}
            }
        ]
    }
}
```



### 2.3 loader打包样式配置

 - <b>那我们想要打包 css ，我们应该怎么配置呢？</b>
    - 首先，先不管原因的下载两个包。 `npm i style-loader css-loader -D`
    - 在配置文件中引入，如下。

`webpack.config.js`

```js
module.exports = {
    mode: 'development',
    entry: './src/index.js',
    output: {
        filename: 'bundle.js'
    },
    module: {
        rules: [
            {
                test: /\.(jpe?g|png|gif)$/,
                use: {
                    loader: 'url-loader'
                    option: {
                    	name: '[name].[ext]',
                    	outputPath: 'images/',
                    	limit: 10000
                	}
            	}
            },
            {
                test: /\.css$/,
                use: ['style-loader', 'css-loader']
            }
        ]
    }
}
```

这时候，我们可以随意编写一个css，并在 入口 js 文件中引入。就会发现有样式引入了。

注意：

> 引入的顺序不能写反。因为 loader 的使用是从后向前的。而工作流程是 css-loader 会把你引入的css模块进行结合打包。生成一个 css，然后 style-loader 对结合后的 css 注入到 header 中，所以要先用 css-loader，因此它要卸载后面。



- <b>除了 css，那 sass , less , stylus 如何打包呢？</b>

<strong>这里就只用 sass 为例了。</strong>

>  我们通过官网发现，我们需要安装 node-sass  和  sass-loader 两个包。
>
> `npm i node-sass sass-loader -D`

 `webpack.config.js`

```js
module.exports = {
    mode: 'development',
    entry: './src/index.js',
    output: {
        filename: 'bundle.js'
    },
    module: {
        rules: [
            {
                test: /\.(jpe?g|png|gif)$/,
                use: {
                    loader: 'url-loader'
                    option: {
                    	name: '[name].[ext]',
                    	outputPath: 'images/',
                    	limit: 10000
                	}
            	}
            },
            {
                test: /\.css$/,
                use: ['style-loader', 'css-loader']
            },
    		{
                test: /\.css$/,
                use: ['style-loader', 'css-loader', 'sass-loader']
            },
        ]
    }
}
```



- <b>浏览器有自己的样式前缀，我们如何加样式前缀呢？</b>

  - 首先，安装 `npm i postcss-loader -D`

  - 安装 `npm i autoprefixer -D` 这个插件

  - ``` js
    // 在根目录下添加一个 postcss.config.js 文件。内容如下。
    module.exports = {
      plugins: {
        require('autoprefixer')
      }
    }
    ```

  - ``` js
    // 在 webpack.config.js 文件中，添加 4 的位置
    module.exports = {
        mode: 'development',
        entry: './src/index.js',
        output: {
            filename: 'bundle.js'
        },
        module: {
            rules: [
                {
                    test: /\.(jpe?g|png|gif)$/,
                    use: {
                        loader: 'url-loader'
                        option: {
                        	name: '[name].[ext]',
                        	outputPath: 'images/',
                        	limit: 10000
                    	}
                	}
                },
                {
                    test: /\.css$/,
                    use: ['style-loader', 'css-loader']
                },
        		{
                    test: /\.css$/,
                    use: [
        				'style-loader',
                        'css-loader',
                        'sass-loader',
                        'post-loader'	// 4.
                    ]
                },
            ]
        }
    }
    ```

> 官方给出 postcss-loader 的加载器。[详情](https://webpack.js.org/loaders/postcss-loader)

#### 2.3.1 样式打包补充

##### importLoaders

##### module

##### 字体文件



### 2.4 dist 下生成 html —— HtmlWebpackPlugin

#### 2.4.1 clean-webpack-plugin

### 2.5 devtool

### 2.6 devServer

### 2.7 Babel

- preset-env
  - useBuiltIns: 'usage'	=> 	import '@babel/polyfill'
- polyfill
- transform-runtime

## 三、webpack 高级配置

### 3.1 Tree Shaking  (只支持 ES Module)

- webpack.config.js 中 添加参数  optimization: { usedExports : true }
- package.json 配置 “sideEffects”: false
- P.S. 生产环境下 Tree Shaking 默认配置

### 3.2 Development 和 Production 模式的区分打包

### 3.3 webpack 和 Code Splitting

- ``` js
  optimization: {
      splitChunks: {
          chunks: 'all'
      }
  }
  ```