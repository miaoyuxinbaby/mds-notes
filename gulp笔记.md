# gulp笔记
- stduy from http://www.cnblogs.com/2050/p/4198792.html
- 在gulpfile.js所在目录下执行gulp命令，gulp后面可以加上要执行的任务名，例如gulp task1，如果没有指定任务名，则会执行任务名为default的默认任务
- 使用gulp，仅需知道4个API即可：gulp.task(),gulp.src(),gulp.dest(),gulp.watch()
### gulp.src()
###### gulp.src(globs[, options])
- gulp.src()方法正是用来获取流的，但要注意这个流里的内容不是原始的文件流，而是一个虚拟文件对象流(Vinyl files)，这个虚拟文件对象中存储着原始文件的路径、文件名、内容等信息
- globs参数是文件匹配模式，用来匹配文件路径(包括文件名)，可以直接指定某个具体的文件路径。当有多个匹配模式时，该参数可以为一个数组。
options为可选参数
* 匹配文件路径中的0个或多个字符，但不会匹配路径分隔符，除非路径分隔符出现在末尾
** 匹配路径中的0个或多个目录及其子目录,需要单独出现，即它左右不能有其他东西了。如果出现在末尾，也能匹配文件。
? 匹配文件路径中的一个字符(不会匹配路径分隔符)
[...] 匹配方括号中出现的字符中的任意一个，当方括号中第一个字符为^或!时，则表示不匹配方括号中出现的其他字符中的任意一个，类似js正则表达式中的用法<br>
###### gulp.dest(path[,options])
- gulp.dest()方法是用来写文件的,path为写入文件的路径
- 要想使用好gulp.dest()这个方法，就要理解给它传入的路径参数与最终生成的文件的关系。
gulp的使用流程一般是这样子的：首先通过gulp.src()方法获取到我们想要处理的文件流，然后把文件流通过pipe方法导入到gulp的插件中，最后把经过插件处理后的流再通过pipe方法导入到gulp.dest()中，gulp.dest()方法则把流中的内容写入到文件中，这里首先需要弄清楚的一点是，我们给gulp.dest()传入的路径参数，只能用来指定要生成的文件的目录，而不能指定生成文件的文件名，它生成文件的文件名使用的是导入到它的文件流自身的文件名，所以生成的文件名是由导入到它的文件流决定的，即使我们给它传入一个带有文件名的路径参数，然后它也会把这个文件名当做是目录名，例如：
```
var gulp = require('gulp');
gulp.src('script/jquery.js')
    .pipe(gulp.dest('dist/foo.js'));
```
###### gulp.task(name[, deps], fn)
- gulp.task方法用来定义任务
- name 为任务名
deps 是当前定义的任务需要依赖的其他任务，为一个数组。当前定义的任务会在所有依赖的任务执行完毕后才开始执行。如果没有依赖，则可省略这个参数
fn 为任务函数，我们把任务要执行的代码都写在里面。该参数也是可选的。

###### gulp.watch(glob[, opts], tasks)
- gulp.watch()用来监视文件的变化，当文件发生变化后，我们可以利用它来执行相应的任务，例如文件压缩等
- glob 为要监视的文件匹配模式，规则和用法与gulp.src()方法中的glob相同。
opts 为一个可选的配置对象，通常不需要用到
tasks 为文件变化后要执行的任务，为一个数组

###### 一些常用的gulp插件
- 要使用gulp的插件，首先得用require来把插件加载进来
1. gulp-file-include
```
var gulp = require('gulp');
var fileinclude  = require('gulp-file-include');

gulp.task('fileinclude', function() {
    // 适配src中所有文件夹下的所有html，排除src下的include文件夹中html
    gulp.src(['src/**/*.html','!src/include/**.html'])
        .pipe(fileinclude({
          prefix: '@@',     //前缀
          basepath: '@file'
        }))
    .pipe(gulp.dest('dist')); // 输出路径
});


// 使用

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title></title>
</head>
<body>
    @@include('./include/header.html')
    <p> 这是 layout 的内容 </p>
    @@include('./include/footer.html')
</body>
</html>
```
