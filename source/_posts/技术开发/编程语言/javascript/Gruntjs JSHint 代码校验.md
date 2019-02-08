---
title: Gruntjs JSHint 代码校验
date: 2014/12/26
tags: [javascript]
---

JSHint跟JSLint非常像，都是Javascript代码验证工具，这种工具可以检查你的代码并提供相关的代码改进意见。  
对于你的代码，你可以选择多种方式来进行检验： 

### 第一种方法
进入JSHint首页，粘贴你的代码，选择相关的选项，然后点击右下角的Lint按钮就可以了。

### 第二种方法
使用Grunt整合的JSHint, 在项目根目录中建立一个grunt.js文件： 

```javascript
module.exports = function (grunt) { 
    'use strict';  
    // Project configuration.
    grunt.initConfig({
        pkg: '<json:package.json>',
        test: {
            files: ['test/**/*.js']
        },
        lint: {
            files: ['grunt.js', 'lib/**/*.js', 'test/**/*.js']
        },
        watch: {
            files: '<config:lint.files>',
            tasks: 'default'
        },
        jshint: {
            options: {
                curly: true,
                eqeqeq: true,
                newcap: true,
                noarg: true,
                sub: true,
                undef: true,
                boss: true,
                node: true
            },
            globals: {
                exports: true
            }
        }
    });  
    // Default task.
    grunt.registerTask('default', 'lint test');
};
```
这里的lint.files就是要验证的所有文件。而下面的jshint.option是jshint的具体配置信息。简单介绍一下：
- **curly**: 大括号包裹，即不能使用这种代码：
```javascript
while (notEnd())
    doSomething();
```
- **eqeqeq**: 对于简单类型，使用===和!==，而不是==和!= 
- **newcap**: 对于首字母大写的函数（声明的类），强制使用new 
- **noarg**: 禁用arguments.caller和arguments.callee 
- **sub**: 对于属性使用aaa.bbb而不是aaa['bbb'] 
- **undef**: 查找所有未定义变量 
- **boss**: 查找类似与if(a = 0)这样的代码 
- **node**: 指定运行环境为node.js 

### 第三种方法
直接使用API： `var result = JSHINT(source, options, globals);`。  
其中source为待检查的脚本字符串（或者字符串数组）。options同第二种方法。globals是指定全局变量。如果验证通过，返回true，否则返回false。如果返回false，那么可以使用JSHINT.errors来查看错误。

参考资料：
[1] 官方白皮书：http://www.jshint.com/docs/ 