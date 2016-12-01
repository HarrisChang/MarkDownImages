## 1、安装node.js
   1.1 gulp是基于node.js的，所以第一步需要安装node.js;
   1.2 打开[nodejs官网](https://nodejs.org/en/)，根据操作系统下载相应版本，然后安装即可；
## 2、npm介绍
   2.1 npm(node package manager)是nodejs的包管理器，用于node插件管理(包括安装、卸载、管理依赖等);
   2.2 使用npm安装插件：命令行执行`npm install <name> [-g] [- -save-dev]`;
   - <name>：node插件名称。例：`npm install gulp-less -g - -save-dev`
   - \-g：全局安装。将会安装在C:\Users\Administrator\AppData\Roaming\npm，并且写入系统环境变量；全局安装可以通过命令行在任何地方调用它，本地安装将安装在定位目录的node_modules文件夹下，通过require()调用；
   - \-\-save：将保存配置信息至package.json（package.json是nodejs项目配置文件）
   - \-dev：保存至package.json的devDependencies节点，不指定-dev将保存至dependencies节点；一般保存在dependencies的像这些express/ejs/body-parser等等。
   - 为什么要保存至package.json？因为node插件包相对来说非常庞大，所以不加入版本管理，将配置信息写入package.json并将其加入版本管理，其他开发者对应下载即可（命令提示符执行npm install，则会根据package.json下载所有需要的包，npm install \-\-production只下载dependencies节点的包）。
   - 使用npm卸载插件：`npm uninstall <name> [-g] [- -save-dev]` PS：不要直接删除本地插件包
  > 删除全部插件：`npm install rimraf \-g` 用法：`rimraf node_modules`或者将插件一个个删除

- 使用npm更新插件：`npm update <name> [-g] [- -save-dev]`
  > 更新全部插件：'npm update [- -save-dev]'

- 查看当前目录已安装插件：`npm list`
- npm安装插件时是从http://registry.npmjs.org下载对应的插件，该网站服务器位于国外，所以国内用户下载较慢，可以添加如下命令：\-\-registry=https://registry.npm.taobao.org
## 3、全局安装gulp
- 全局安装gulp是为了通过它执行gulp任务
- 输入命令行`npm install gulp -g`
- 使用`gulp -v`查看gulp版本
## 4、新建package.json文件
在项目根目录输入`npm init`然后输入相关信息即可
## 5、本地安装gulp和所需插件
输入命令行`npm install gulp --save-dev`
## 6、新建gulpfile.js文件
gulpfile.js是gulp项目的配置文件，一般放置于根目录

## 7、通过命令行提示符运行gulp

以下是我常用的gulpfie.js配置，用于常规项目已经足够，供参考。
/* 载入插件 */
var gulp = require('gulp'),  //基础库
    less = require('gulp-less'),  //编译less插件
    cleancss = require('gulp-clean-css'),  //压缩css
    uglify = require('gulp-uglify'),  //压缩js
    concat = require('gulp-concat'),   //合并文件
    rename = require('gulp-rename'),  //重命名
    clean = require('gulp-clean');    //清空文件夹

/* 将node_modules目录中的文件对应到指定位置 */
gulp.task('buildLib',function(){
    gulp.src('./node_modules/jquery/dist/jquery.min.js')
        .pipe(gulp.dest('./lib/js'));
});

/* less解析 */
gulp.task('build-less',function(){
    //编译less目录下的所有less文件，不包含子文件夹中的less文件
    gulp.src('./src/less/*.less')
        .pipe(less())
        .pipe(gulp.dest('./src/css'));
});

/* 合并、压缩、重命名css */
gulp.task('stylesheets',['build-less'],function(){
    gulp.src(['./src/css/*.css','!./src/css/all.css','!./src/css/all.min.css'])
        .pipe(concat('all.css'))
        .pipe(gulp.dest('./src/css/'))
        .pipe(cleancss())
        .pipe(rename({suffix : '.min'}))
        .pipe(gulp.dest('./src/css'))
});

/* 合并、压缩js文件 */
gulp.task('javascripts',function(){
    gulp.src(['./src/js/*.js','!./src/js/all.js','!./src/js/all.min.js'])
        .pipe(concat('all.js'))
        .pipe(gulp.dest('./src/js'))
        .pipe(rename({suffix:'.min'}))
        .pipe(uglify())
        .pipe(gulp.dest('./src/js'))
});

/* 清空目标文件夹 */
gulp.task('clean',function(){
    //read参数为false表示不读取文件的内容
    return gulp.src(['./dist/**/','./dist/**.**'],{read:false})
        .pipe(clean({force:true}));
});


/* 设置默认任务 */
gulp.task('default',['buildLib','build-less'],function(){
    //监听less目录下的所有less文件，包含子文件夹中的less文件，当less文件改变则执行build-less
    gulp.watch('./src/less/**/*.less',['build-less']);
    console.log("gulp run");
});

//定义一个prod任务作为运行或发布时使用
gulp.task('prod',['clean','javascripts','stylesheets'],function(){
    gulp.watch('./src/less/**/*.less',['stylesheets'])
    console.log("任务开始运行");
});
</pre>