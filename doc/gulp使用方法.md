使用gulp有挺长一段时间了，起初只是为了用less编译css，后来慢慢拓展到压缩文件以及浏览器自动刷新等。到现在，自己已经配置了一套基本齐全的gulpfile文件，能满足小型项目开发所需的功能，文中会一一提到，这里就不再赘诉。
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
  > 更新全部插件：`npm update [- -save-dev]`

- 查看当前目录已安装插件：`npm list`
- npm安装插件时是从http://registry.npmjs.org 下载对应的插件，该网站服务器位于国外，所以国内用户下载较慢，可以添加如下命令： \-\-registry=https://registry.npm.taobao.org
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
                    less = require('gulp-less'), //编译less
                    postcss = require('gulp-postcss'),  //
                    postcssCssnext = require('postcss-cssnext'),  //
                    browserSync = require('browser-sync').create(),  //
                    useref = require('gulp-useref'),  //
                    filter = require('gulp-filter'),  //
                    csso = require('gulp-csso'),  //
                    uglify = require('gulp-uglify'),  //
                    rev = require('gulp-rev'),  //加md5后缀
                    revReplace = require('gulp-rev-replace');  //替换引用的加了md5后缀的文件
                var processors = [
                    postcssCssnext
                ];  //该数组将插入我们想使用的PostCss插件

                /* less编译、css加前缀 */
                gulp.task('buildCss',function(){
                    //编译less目录下的所有less文件，不包含子文件夹中的less文件
                    gulp.src('./src/less/*.less')
                        .pipe(less())
                        .pipe(postcss(processors))
                        .pipe(gulp.dest('./src/css'))
                        .pipe(browserSync.stream());  //return stream以保证browserSync.reload在正确的时机调用
                });

                /* 浏览器自动刷新 */
                gulp.task('sync',['buildCss'],function(){
                    browserSync.init({
                        server: "./src"  //初始化项目根目录为：'./src'，也可以使用代理(proxy: 'yourlocal.dev')
                    });
                    gulp.watch('./src/less/**/*.less',['buildCss']);  //监听less目录下的所有文件，包含子文件夹中的less文件，当less文件改变时执行buildLess
                    gulp.watch(['./src/**/*.html','./src/**/*.js']).on('change',browserSync.reload);  //监听html,js文件的变化，自动重新载入
                });

                /* 定义默认任务作为开发时使用(包含如下功能) */
                //1.自动编译css；
                //2.自动给css加前缀；
                //3.当src目录下任何文件变动时自动刷新浏览器页面；
                gulp.task('default',['sync'],function(){
                    console.log('developing...');
                });

                /* 定义一个生产任务prod用作打包发布时使用 */
                gulp.task('production',function(){
                    console.log('production module');
                    var jsFilter = filter('**/*.js',{restore: true}),
                        cssFilter = filter('**/*.css',{restore: true}),
                        indexHtmlFilter = filter(['**/*','!**/index.html'],{restore: true});
                    return gulp.src('src/index.html')
                        .pipe(useref())
                        .pipe(jsFilter)
                        .pipe(uglify())
                        .pipe(jsFilter.restore)
                        .pipe(cssFilter)
                        .pipe(csso())
                        .pipe(cssFilter.restore)
                        .pipe(indexHtmlFilter)
                        .pipe(rev())
                        .pipe(indexHtmlFilter.restore)
                        .pipe(revReplace())
                        .pipe(gulp.dest('dist'));
                });

如有疑问，欢迎留言咨询或发mail给我~

