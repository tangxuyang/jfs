## jQuery From Scratch

### clone
```
git clone http://github.com/jquery/jquery
```
### build
```
cd jquery && npm run build
```
简单的一条命令就你就体验了一般自己构建jquery的过程，不信你去dist目录里面看看！  
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
### 