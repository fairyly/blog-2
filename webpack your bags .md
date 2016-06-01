**Webpack your bags** 
原文： [**Webpack your bags**](https://blog.madewithlove.be/post/webpack-your-bags/ "Webpack your bags")(by [Maxime Fabre](https://twitter.com/anahkiasen))

2016年5月28日  Du Liang 翻译。


![](https://webpack.github.io/assets/what-is-webpack.png)
# Webpack your bags #
之前你可能已经听说过这个很酷的叫webpack工具，如果你没仔细了解过这个工具，你可能会有些困惑，因为有人说他它是像**Gulp**之类构建工具，也有人说它是像**Browserify**之类的bundler，如果你仔细了解一下，你可能还是会不明白是怎么回事儿，因为官网上把webpack说成是这两者。

说实话，开始的时候对于**“webpack到底是什么“很模糊，感觉很受挫，然后我直接就把网页关了，毕竟我已经有一个构建系统了，而且我用得也非常哈皮，如果你紧赶javascript的时髦的话，像我这样，你可能在估计很快因为在各种流行的东西中频繁地跳来跳去而烧的灰飞烟灭了。
现在我有些经验了，我觉得我应该写这篇文章给那些还处在混沌中的小伙伴儿，更清楚解释一下webpack到底是什么，更重要的是它的什么地方那么棒以至于值得我们投入更多的精力。

## Webpack是什么？ ##
好的，下面我们来回答一下，介绍里面提到的那个问题，Webpack是一个构建系统还是一个模块bundler（捆绑器），恩-，两个都是 - 我不是说它两个事儿都干，我的意思是它把两者连接起来了，webpack不是先构建你的资源（assets），然后再bundle你的模块，它把你的资源本身当做模块。

更准确的说，它不是先编译你的所有的Sass文件，再优化所有的图片，再在一个地方include进来，然后bundle所有的模块，在另一个地方include到你的page上。假设你在是下面这样：

    import stylesheet from 'styles/my-styles.scss';
    import logo from 'img/my-logo.svg';
    import someTemplate from 'html/some-template.html';
    
    console.log(stylesheet); // "body{font-size:12px}"
    console.log(logo); // "data:image/svg+xml;base64,PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0iVVRGLTgiIHN0YW5kYWxvbmU9Im5[...]"
    console.log(someTemplate) // "<html><body><h1>Hello</h1></body></html>"

你的所有的资源本身被当成模块，这些模块可以被import，modify，manipulate（操作），最后被打包到你最后的bundle

为了达到这个目的，得在webpack configuration里注册loaders，loader就是当你遇到某种文件的时候，对它做相应的一种处理的一种插件，下面是一些loader的例子：
    
    {
      // When you import a .ts file, parse it with Typescript
      test: /\.ts/,
      loader: 'typescript',
    },
    {
      // When you encounter images, compress them with image-webpack (wrapper around imagemin)
      // and then inline them as data64 URLs
      test: /\.(png|jpg|svg)/,
      loaders: ['url', 'image-webpack'],
    },
    {
      // When you encounter SCSS files, parse them with node-sass, then pass autoprefixer on them
      // then return the results as a string of CSS
      test: /\.scss/,
      loaders: ['css', 'autoprefixer', 'sass'],
    }

最后在食物链的终点所有的loader返回的都是string，这样可以使webpack把资源最后包装成javascript模块。这个例子里你的Sass文件被loaders转换后，里面看起来差不多是这样。

    export default 'body{font-size:12px}';

![](http://i.imgur.com/iK7sL6Z.gif)
## 那你究竟为什么要用它呢？ ##
一旦你明白webpack是做什么的，很有可能就会想到第二个问题：用它能有什么好处呢？images和CSS？还在我的JS里？玩啥呢哥们儿。好的，看下这个：很长时间我一直被告诉要把所有文件都放在一个文件里，这样来保证不浪费我们的request。

但这样导致一个最大的缺点，就是现在大多数人都把所有的资源（assets）bundle到一个单独的app.js文件里，然后把他include每一个页面，这就意味着渲染任意一个页面的时候大部分时间都浪费在加载一大堆根本用不上的资源上。如果你不这么做，那么你很有可能得手动把这些资源include到指定的页面，这就会引出一大团乱遭的依赖树去维护和跟踪如：哪些页面这个需要依赖？哪些页面style A 和Style B 起作用。

这两个方法都不对，也都不是错的。我们把webpack当做一个中间者-他不仅仅是一个构建（build）系统或者一个bundler，它是一个邪恶小精灵般的智能打包（packing）系统，正确地配置后，它甚至比你都了解你的系统栈（stack），而且它比你清楚怎么样最优优化你的系统。


## 我们来做一个小的app ##

为了让你更容易地理解webpack带来的好处，我们做一个小的app，然后用它来bundle 资源（assets），在本篇文章里我建议用Node 4 (或 5)和NPM 3因为扁平的依赖树在你用webpack的时候会避免很多让人头疼的麻烦，如果你还没有NPM3,你可以通过 `npm install npm@3 -g` 来安装。

    $ node --version
    v5.7.1
    $ npm --version
    3.6.0

同时我也建议你把 `node_modules/.bin` 加到你的PATH variable（环境变量里）以避免每次都手动打 `node_modules/.bin/webpack` ，后面的所有例子都不会显示我要执行的命令行的 `node_modules/.bin` 部分。

**基本引导**
我们开始，先创建个project，安装上webpack，我们也加上jquery好在后面证明一些东西。

    $ npm init -y
    $ npm install jquery --save
    $ npm install webpack --save-dev
现在来我们创建一个app入口，用纯ES5 （in plain ES5）

**src/index.js**

    var $ = require('jquery');
    
    $('body').html('Hello');


在一个webpack.config.js文件里建webpack configuration，webpack configuration是javascript，需要export 一个object

**webpack.config.js**

    module.exports = {
	    entry:  './src',
	    output: {
		    path: 'builds',
		    filename: 'bundle.js',
	    },
    };
    
这里，entry告诉webpack那个文件是你的app的入口点。这些是你的主文件，他们在依赖树的顶端，然后我们告诉它编译我们的bundle 到`bundle.js`文件放在`builds`目录下，再创建相应的index HTML


    <!DOCTYPE html>
    <html>
	    <body>
		    <h1>My title</h1>
		    <a>Click me</a>
		    
		    <script src="builds/bundle.js"></script>
	    </body>
    </html>


 跑一下webpack，如果一切正常，会看见一个信息告诉我们正确地编译了`bundle.js`

    $ webpack
    Hash: d41fc61f5b9d72c13744
    Version: webpack 1.12.14
    Time: 301ms
    AssetSize  Chunks Chunk Names
    bundle.js  268 kB   0  [emitted]  main
       [0] ./src/index.js 53 bytes {0} [built]
    + 1 hidden modules
  
这里webpack告诉你你的bundle.js包含了我们的入口点（index.js）和一个隐藏的模块，这个就是jquery，默认情况下webpack把不是你的模块会隐藏掉，如果想看见webpack编译的所有的模块 ，我们可以加`--display-modules` 参数

    $ webpack --display-modules
    bundle.js  268 kB   0  [emitted]  main
       [0] ./src/index.js 53 bytes {0} [built]
       [1] ./~/jquery/dist/jquery.js 259 kB {0} [built]

你也可以运行 `webpack --watch` 来自动监视文件的变化，如果有变化，自动重新编译。
搭建我们的第一个loader
现在还记得我们怎么讨论webpack可以导入CSS和HTML还有各种文件的吗？什么时候能排上用场呢？好的，如果你跟随着过去几年朝着web组件方向的大跃进（Angular 2, Vue, React, Polymer, X-Tag, etc.）估计你听过这样一个观点：你的app是如果用一套可重用，自包含的UI组件搭建的话相比较于一个单独的内聚的UI将会变得更容易维护，这个可重用的组件就是web 组件（在这里我说的比较简单），现在为了让组件变成真正的自包含，需要把组件需要的所有的东西封装到他们自己的内部，我们来考虑一个button，它里面肯定有一些HTML，而且还有一些JS来保证它的交互性，还得来一些style，这些东西如果需要的时候一起加载进来会非常棒，只有在我们导入button组件的时候，我们才会得到所有的这些资源文件（asset）。

来我们写一个button，首先我先假设你们已经熟悉了ES2015 ，我们先加入babel这个loader。想要在webpack里安装一个loader，有两步需要做：1.`npm install {whatever}-loader`,2.把它加到你的Webpack configuration里的`module.loaders`部分，我们开始，现在我们要加babel，所以：

    $ npm install babel-loader --save-dev

我们也得安装	Babel ，因为现在我们这个case，loader不会安装它，我们需要装`babel-core package` 和 `es2015 preset`:

    $ npm install babel-core babel-preset-es2015 --save-dev.

我们现在创建`.babelrc`文件，告诉bable用那个preset，这是一个json文件让你配置Babel在你的code上执行哪种变体，我们现在这个我们我告诉它用`es2015 `preset

**.babelrc** `{ "presets": ["es2015"] }`

现在babel配完了，我们可以更新我们的配置：我们想要什么？我们想babel在我们所有的.js结尾的文件上运行，**但是**由于webpack遍历所有的依赖，我们想避免babel运行在第三方代码上，如jquery，所以我们可以再稍微过滤一下，loaders既可以有`include`也可以由`exclude`，可以是string，regex（正则表达式），一个callback（回调），随便用哪个。因为我们想让babel值运行在我们的文件上，所以我们只include我们自己的source目录：
    
    module.exports = {
	    entry:  './src',
		    output: {
			    path: 'builds',
			   	 filename: 'bundle.js',
			    },
			    module: {
			    loaders: [
			    {
				    test:   /\.js/,
				    loader: 'babel',
				    include: __dirname + '/src',
			    }
		    ],
	    }
    };
    
我们可以用ES6重写一下我们的`index.js`这个小文件，由于我们引入了babel，从这里开始后面的所有例子都用ES6.

    import $ from 'jquery';
    
    $('body').html('Hello');


**写个小组件**
我们来写一个小的Button 组件，它有一些SCSS style，一个HTML template，和一些behavior，那么我们安装一些我们需要的东西。首先我们用一个非常轻量的模板包Mustache ，我们也需要给Sass和HTML用的loaders，因为结果会通过管道（pipe）从一个loader传到另一个，我们需要一个Sass loader，现在一旦我们有了css，会有多种方式处理它，当前阶段，我们用一个叫`style-loader`的loader，它会接收一段CSS，然后动态地把它插入到页面。

    $ npm install mustache --save
    $ npm install css-loader style-loader html-loader sass-loader node-sass --save-dev

为了让webpack用管道（pipe）把东西从一个loader传到另一个loader，我们简传入几个loader方向由右到左，用一个`！`分开，或者你可以用一个数组通过`loaders`属性，不是`loader`

    {
	    test:/\.js/,
	    loader:  'babel',
	    include: __dirname + '/src',
    },
    {
    test:   /\.scss/,
	    loader: 'style!css!sass',
	    // Or
	    loaders: ['style', 'css', 'sass'],
    },
    {
	    test:   /\.html/,
	    loader: 'html',
    }

现在我们把loaders准备好了，我们来写一个button：

**src/Components/Button.scss**

	.button {
	  background: tomato;
	  color: white;
	}

**src/Components/Button.html**

    <a class="button" href="{{link}}">{{text}}</a>

**src/Components/Button.js**

	
	import $ from 'jquery';
	import template from './Button.html';
	import Mustache from 'mustache';
	import './Button.scss';
	
	import $ from 'jquery';
	import template from './Button.html';
	import Mustache from 'mustache';
	import './Button.scss';
	
	export default class Button {
			constructor(link) {
			this.link = link;
		}
		
		onClick(event) {
			event.preventDefault();
			alert(this.link);
		}
		
		render(node) {
			const text = $(node).text();
		
			// Render our button
			$(node).html(Mustache.render(template, {text}));
		
			// Attach our listeners
			$('.button').click(this.onClick.bind(this));
		}
	}

你的Button现在是百分之百自包含的，无论什么时候导入，无论运行在什么context，它有所有需要的东西，然后正确地render（渲染），现在我们只需要把我们的Button render到我们的网页上（page）：

**src/index.js**

    js import Button from ‘./Components/Button’;
    
    const button = new Button(‘google.com’); button.render(‘a’); 
   
我们来运行一下webpack，然后刷新一下页面，你应该就能看见你的挺丑的button出来了。
![](http://i.imgur.com/8Ov1x2P.png)

现在你已经学了怎么配置loader和怎么给你的app的每一个部分定义dependency（依赖），现在可能看起来不太重要这些，但现在我们来把我们的例子再做一下改进：

code 分片（splitting）

这个例子还不错什么都有，但可能我们不总需要我们的Button，可能有些界面不会有 `a`（link）来让我们渲染Button用，在这中情况下，我们不需要导入所有的Button的style（式样），template（模板），Mustache 和一切相关的东西，对吧？“Monolithic bundle”（单片绑定） VS “Unmaintainable manual imports”（不好维护的手动导入）. 这种idea是，你在你的代码里定义split point（分割点）：你的code中可以轻松拆分开到单独文件中的部分，然后按需加载，语法非常简单：

    import $ from 'jquery';
    
    // This is a split point
    require.ensure([], () => {
      // All the code in here, and everything that is imported
      // will be in a separate file
      const library = require('some-big-library');
      $('foo').click(() => library.doSomething());
    });

在`require.ensure` callback 里的任何东西都会被拆分成chunk（代码块）-一个webpack需要的时候会通过ajax单独加载的bundle，这意味着我们基本上有这些：
    
    bundle.js
    |- jquery.js
    |- index.js // our main file
    chunk1.js
    |- some-big-libray.js
    |- index-chunk.js // the code in the callback
    
    
 你无需导入`chunk1.js`。webpack只有在需要它的时候才会加载它，这意味着你可以把你的code按照各种逻辑分成多块，我们将在下面的例子中这么做，我们只想在page里有link的的时候才需要Button

**src/index.js**

    if (document.querySelectorAll('a').length) {
	    require.ensure([], () => {
		    const Button = require('./Components/Button').default;
		  	  const button = new Button('google.com');
		    
		    button.render('a');
	    });
    }
    
注意，用`require`的时候，如果你想default export 你需要通过`.default`手动获取它，
因为`require `不会同时处理default 和 normal（正常） exports，所以你必须指定返回哪个，不过`import`有一个系统处理这个，所以它已经知道了(例如： `import foo from 'bar'`)vs `import {baz} from 'bar').`

webpack的output现在应该会相应地不同了，我们来用`--display-chunks` flag 来运行一下，来看一下哪个模块在哪个chunk里
    
    $ webpack --display-modules --display-chunks
    Hash: 43b51e6cec5eb6572608
    Version: webpack 1.12.14
    Time: 1185ms
      Asset Size  Chunks Chunk Names
      bundle.js  3.82 kB   0  [emitted]  main
    1.bundle.js   300 kB   1  [emitted]
    chunk{0} bundle.js (main) 235 bytes [rendered]
    [0] ./src/index.js 235 bytes {0} [built]
    chunk{1} 1.bundle.js 290 kB {0} [rendered]
    [1] ./src/Components/Button.js 1.94 kB {1} [built]
    [2] ./~/jquery/dist/jquery.js 259 kB {1} [built]
    [3] ./src/Components/Button.html 72 bytes {1} [built]
    [4] ./~/mustache/mustache.js 19.4 kB {1} [built]
    [5] ./src/Components/Button.scss 1.05 kB {1} [built]
    [6] ./~/css-loader!./~/sass-loader!./src/Components/Button.scss 212 bytes {1} [built]
    [7] ./~/css-loader/lib/css-base.js 1.51 kB {1} [built]
    [8] ./~/style-loader/addStyles.js 7.21 kB {1} [built]


可以看到我们的入口（`bundle.js`）现在只有一些webpack的逻辑，其它的东西(jQuery, Mustache, Button) 都在`1.bundle.js `,只有page上有anchor （锚：超链接的意思）的时候才会加载，为了让webpack知道用ajax加载的时候在哪能找到chunks，我们必须在我们的配置中加几行:
    
    path:   'builds',
    filename:   'bundle.js',
    publicPath: 'builds/',
    



`output.publicPath`选项告诉webpack相对于当前的page（我们的case是在 /builds/）在哪能找到built（构建后的）assets （资源），现在访问我们的page我们会看到一切都正常工作，但更重要的是，我们能看到，由于页面上有anchor （锚：超链接的意思），webpack实时地加载加载了chunk：

![](http://i.imgur.com/rPvIRiB.png)

如果我们page上没有anchor，只有`bundle.js`会被加载 ，这点可以让你智能地把你的app里的大片逻辑拆分开，让每个页面值加载它真正需要的，我们也可以给我们的split point命名，不用`1.bundle.js`，我们可以用更有语义的名字，你可以通过传给`require.ensure`第三个参数来指定:
    
    require.ensure([], () => {
	    const Button = require('./Components/Button').default;
	    const button = new Button('google.com');
	    
	    button.render('a');
    }, 'button');

这样就会生成`button.bundle.js`而不是`1.bundle.js.`
加入第二个组件
现在啥都有了非常cool了已经，我们再来加一个组件来看看好不好使：

**src/Components/Header.scss**
    
    .header {
      font-size: 3rem;
    }

**src/Components/Header.html**

    <header class="header">{{text}}</header>
**src/Components/Header.js**
	
	import $ from 'jquery';
	import Mustache from 'mustache';
	import template from './Header.html';
	import './Header.scss';
	
	export default class Header {
		render(node) {
			const text = $(node).text();
			
			$(node).html(
				Mustache.render(template, {text})
			);
		}
	}

我们在我们的application里把它render（渲染）一下：

    // If we have an anchor, render the Button component on it
    if (document.querySelectorAll('a').length) {
	    require.ensure([], () => {
		    const Button = require('./Components/Button');
		    const button = new Button('google.com');
		    
		    button.render('a');
	    });
    }
    
    // If we have a title, render the Header component on it
    if (document.querySelectorAll('h1').length) {
		    require.ensure([], () => {
		    const Header = require('./Components/Header');
		    
		    new Header().render('h1');
	    });
    }

现在用`--display-chunks --display-modules` flags（参数标记）来看一下webpack的output：


	$ webpack --display-modules --display-chunks
	Hash: 178b46d1d1570ff8bceb
	Version: webpack 1.12.14
	Time: 1548ms
	  Asset Size  Chunks Chunk Names
	  bundle.js  4.16 kB   0  [emitted]  main
	1.bundle.js   300 kB   1  [emitted]
	2.bundle.js   299 kB   2  [emitted]
	chunk{0} bundle.js (main) 550 bytes [rendered]
	[0] ./src/index.js 550 bytes {0} [built]
	chunk{1} 1.bundle.js 290 kB {0} [rendered]
	[1] ./src/Components/Button.js 1.94 kB {1} [built]
	[2] ./~/jquery/dist/jquery.js 259 kB {1} {2} [built]
	[3] ./src/Components/Button.html 72 bytes {1} [built]
	[4] ./~/mustache/mustache.js 19.4 kB {1} {2} [built]
	[5] ./src/Components/Button.scss 1.05 kB {1} [built]
	[6] ./~/css-loader!./~/sass-loader!./src/Components/Button.scss 212 bytes {1} [built]
	[7] ./~/css-loader/lib/css-base.js 1.51 kB {1} {2} [built]
	[8] ./~/style-loader/addStyles.js 7.21 kB {1} {2} [built]
	chunk{2} 2.bundle.js 290 kB {0} [rendered]
	[2] ./~/jquery/dist/jquery.js 259 kB {1} {2} [built]
	[4] ./~/mustache/mustache.js 19.4 kB {1} {2} [built]
	[7] ./~/css-loader/lib/css-base.js 1.51 kB {1} {2} [built]
	[8] ./~/style-loader/addStyles.js 7.21 kB {1} {2} [built]
	[9] ./src/Components/Header.js 1.62 kB {2} [built]
	   [10] ./src/Components/Header.html 64 bytes {2} [built]
	   [11] ./src/Components/Header.scss 1.05 kB {2} [built]
	   [12] ./~/css-loader!./~/sass-loader!./src/Components/Header.scss 192 bytes {2} [built]

