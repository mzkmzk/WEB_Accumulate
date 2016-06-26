# Grunt

作用: 压缩 编译 单元测试 linting > 自动化

##1. 基础
###1.1 起步

1. 先确认下npm为最新版本`sudo npm update -g npm`
2. 安装Grunt命令行(CLI)到全局环境`npm install -g grunt-cli`

###1.2 CLI如何工作的

运行`grunt`,利用node提供的`require()`找到系统安装的Grunt,然后加载,然后传递`Gruntfile`文件的信息,源代码<https://github.com/gruntjs/grunt-cli/blob/master/bin/grunt>

###1.3 找个案例实践下

笔者下载了jQuery<https://github.com/jquery/jquery/tree/2.2-stable>

1. cd到项目目录
2. `npm install`安装项目依赖库
3. `grunt` / `grunt --help`命令

###1.4 自己动手

在项目根目录下 .创建

1. package.json

    内容为: 添加grunt开发环境
    ```javascript
    {
      "name": "my-project-name",
      "version": "0.1.0",
      "devDependencies": {
            "grunt": "~0.4.5",
            "grunt-contrib-jshint": "~0.10.0",
            "grunt-contrib-nodeunit": "~0.4.1",
            "grunt-contrib-uglify": "~0.5.0"
        }
    }
    ```
    
    在pachage.json添加插件文件`npm install <module> --save-dev`,插件列表<http://www.gruntjs.net/plugins>
    
    补充说明<https://docs.npmjs.com/files/package.json>
    
2. Gruntfile.js

    大概结构
    ```javascript
    module.exports = function(grunt) {
        
          // Project configuration.
          grunt.initConfig({
            pkg: grunt.file.readJSON('package.json'),
            uglify: {
              options: {
                banner: '/*! <%= pkg.name %> <%= grunt.template.today("yyyy-mm-dd") %> */\n'
              },
              build: {
                src: 'src/<%= pkg.name %>.js',
                dest: 'build/<%= pkg.name %>.min.js'
              }
            }
          });
        
          // 加载包含 "uglify" 任务的插件。
          grunt.loadNpmTasks('grunt-contrib-uglify');
        
          // 默认被执行的任务列表。
          grunt.registerTask('default', ['uglify']);
        
    };
    ```