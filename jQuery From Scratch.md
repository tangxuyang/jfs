## jQuery From Scratch

### clone
```
git clone http://github.com/jquery/jquery
```
### build
```
cd jquery && npm run build
```
简单的一条命令就你就体验了一把自己构建jquery的过程，不信你去dist目录里面看看！  
但是我们的旅程才刚刚开始，我们要做的是，从无到有的敲出jquery的每一行代码。

### 创建自己的目录myjquery
```
cd ..
mkdir myjquery
```

### npm init
```
npm init -y
```

创建一个package.json文件，用来管理我们的依赖包。

### 创建jquery的目录结构
```
mkdir src dist build test external 
```
我们暂时只会用到src、dist和build。  
如果以后有时间有精力可以看一下test，这个是很有帮助的！
### 创建Gruntfile.js
jquery使用grunt作为构建工具的，是不是觉得已经跟不上潮流了（gulp、webpack、browserify），要你管^_^. 

```
echo '' > Gruntfile.js
```

接下来安装基本的两个包——grunt和load-grunt-tasks
```
npm install --save-dev grunt load-grunt-tasks
```
### 填充我们的Gruntfile.js
```
module.exports = function(grunt){
	grunt.initConfig({
	
	});
	
	require('load-grunt-tasks')(grunt);
	
	grunt.registerTask('default', function(){
		console.log("动手撸jQuery源码！");		
	});
};
```
好了让我们运行一下: grunt

输出结果如下：
```
Running "default" task
动手撸jQuery源码！

Done.
```

让我们开始jQuery撸码之旅吧！！明天再继续（2018-03-20）
### 把基本构建功能添加进来

这次步子有点大，需要的代码有点多，主要目的是把build添加进来！
```
// Gruntfile.js
module.exports = function(grunt){
	
	grunt.initConfig({
		pkg: grunt.file.readJSON("./package.json"),
		build: {
			all: {
				dest: "dist/jquery.js",
				minimum: [
					"core",
					"selector"
				],

				removeWith: {
					ajax: ["manipulation/_evalUrl", "event/ajax"],
					callbacks: ["deferred"],
					css: ["effects", "dimensions", "offset"],
					deferred: {
						remove: ["ajax", "effects", "queue", "core/ready"],
						include: ["core/ready-no-deferred"]
					},
					sizzle: ["css/hiddenVisibleSelectors", "effects/animatedSelector"]
				}
			}
		}
	});

	require("load-grunt-tasks")(grunt);

	grunt.loadTasks("build/tasks");

	grunt.registerTask("default", ["build:*:*"]);

};
```
build任务的设置有点复杂，暂时我也看不懂是干啥的，要想说清楚这个，需要把build/tasks/build.js看一遍，我觉得还是先放一放好了。

然后把build/tasks/build.js从jquery的目录拷贝一份到myjquery中

build/tasks/build.js用到了requirejs和insight，因此需要安装一下：
```
npm install --save-dev requirejs insight
```

build/tasks/build.js还用到了wrapper.js和query.js,他们在src目录下，拷贝过来吧！

运行grunt,我们会得到如下的错误：
```
Running "build:all:*" (build) task
Warning: Error: ENOENT: no such file or directory, open 'xxxxxx\core.js'
In module tree:
    jquery
 Use --force to continue.

Aborted due to warnings.
```

为什么会有这个错误呢？打开src/jquery.js就一目了然了
```
define( [
	"./core",
	"./selector",
	"./traversing",
	"./callbacks",
	"./deferred",
	"./deferred/exceptionHook",
	"./core/ready",
	"./data",
	"./queue",
	"./queue/delay",
	"./attributes",
	"./event",
	"./event/focusin",
	"./manipulation",
	"./manipulation/_evalUrl",
	"./wrap",
	"./css",
	"./css/hiddenVisibleSelectors",
	"./serialize",
	"./ajax",
	"./ajax/xhr",
	"./ajax/script",
	"./ajax/jsonp",
	"./ajax/load",
	"./event/ajax",
	"./effects",
	"./effects/animatedSelector",
	"./offset",
	"./dimensions",
	"./deprecated",
	"./exports/amd",
	"./exports/global"
], function( jQuery ) {

"use strict";

return jQuery;

} );

```
可以看出来jquery.js只是一个入口文件，它本身并不提供任何功能，只是把需要的模块引进来，这里不得不说jquery的模块分的真好，同时也要感叹一下，jquery的构建过程真是太复杂了，完全是自己定制化的，反正我现在是看不懂！

当前，我们还没打算开始引入任何模块，因此，把jquery.js中的引入的模块都注释掉好了，以后我们引入一个打开一个注释，慢慢来。再次运行grunt，成功了：
```
Running "build:all:*" (build) task
>> File 'dist/jquery.js' created.

Done.
```

此时dist下会多出一个文件jquery.js。内容如下：
```
/*!
 * jQuery JavaScript Library v1.0.0
 * https://jquery.com/
 *
 * Includes Sizzle.js
 * https://sizzlejs.com/
 *
 * Copyright JS Foundation and other contributors
 * Released under the MIT license
 * https://jquery.org/license
 *
 * Date: 2018-03-21T08:36Z
 */
( function( global, factory ) {

	"use strict";

	if ( typeof module === "object" && typeof module.exports === "object" ) {

		// For CommonJS and CommonJS-like environments where a proper `window`
		// is present, execute the factory and get jQuery.
		// For environments that do not have a `window` with a `document`
		// (such as Node.js), expose a factory as module.exports.
		// This accentuates the need for the creation of a real `window`.
		// e.g. var jQuery = require("jquery")(window);
		// See ticket #14549 for more info.
		module.exports = global.document ?
			factory( global, true ) :
			function( w ) {
				if ( !w.document ) {
					throw new Error( "jQuery requires a window with a document" );
				}
				return factory( w );
			};
	} else {
		factory( global );
	}

// Pass this if window is not defined yet
} )( typeof window !== "undefined" ? window : this, function( window, noGlobal ) {

// Edge <= 12 - 13+, Firefox <=18 - 45+, IE 10 - 11, Safari 5.1 - 9+, iOS 6 - 9.1
// throw exceptions when non-strict code (e.g., ASP.NET 4.5) accesses strict mode
// arguments.callee.caller (trac-13335). But as of jQuery 3.0 (2016), strict mode should be common
// enough that all such attempts are guarded in a try block.
"use strict";



return jQuery;
} );

```

就是把wrapper.js的内容照搬过去了，不过还是有修改的，把插入点那里的注释干掉了。构建出来的jquery.js没有任何模块，因此没有任何功能。如果你把这个文件加载到html文件中，浏览器里会报错的，说jQuery找不到，因为它直接返回了jQuery，但是jQuery却没地方定义。

#### 说下load-grunt-tasks是干啥的
如果没有load-grunt-tasks,我们要加载grunt-*的话，是这样的：grunt.loadNpmTasks("grunt-contrib-xxx");，如果用了好多个，那就要写好多次。程序员就是为了解决重复而生的，因此就有人创建了load-grunt-tasks这样一个npm包，它会读取package.json中的dependencies、dev-dependencies等依赖节点，然后自动帮我们调用loadNpmTasks。


这样我们就把基本的构建功能添加进来了，然后就是慢慢的引入各个模块。再然后就是把其他的构建任务也引入进来，比如lint，压缩等。

### 引入core
首先把src/jquery.js中的core注释放开，运行grunt
```
Warning: Error: ENOENT: no such file or directory, open 'xxxxxxx\src\core.js'
In module tree:
    jquery
 Use --force to continue.

Aborted due to warnings.
```
接下来是拷贝文件的过程了，如果喜欢，可以依照源码手敲:)(2018-03-21)
