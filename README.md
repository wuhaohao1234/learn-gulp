# learn-gulp
学习gulp的文档

## 学习

到docs目录

## 快速上手

1. 项目初始化

`yarn init -y`

2. 添加gulp到本地分支

`yarn add gulp -D`

3. 编写gulpfile.js

```
var gulp = require('gulp')

gulp.task('default', () => {
   console.log('hello,world')
});
```

代码做了一些事

   * 引入gulp
   * gulp内部有一个函数task，为一个任务
   * task接收两个参数: 参数名, 回调函数
   * 这里写为default 与 打印出hello

4. 运行

`npx gulp`

这里会自动找本地安装的gulp运行

gulp会找到项目中的gulpfile.js

原本命令应该写为 npx gulp 任务名

这里由于没写，所以采用默认的default任务

打印出hello world

Did you forget to signal async completion 这句话是是否通知已经完成，下文有介绍

## What Is Gulp?

gulp.js is a build system, meaning that you can use it to automate common tasks in the development of a website. It’s built on Node.js, and both the Gulp source and your Gulp file, where you define tasks, are written in JavaScript (or something like CoffeeScript, if you so choose). This makes it perfect if you’re a front-end developer: You can write tasks to lint your JavaScript and CSS, parse your templates, and compile your LESS when the file has changed (and these are just a few examples), and in a language that you’re probably already familiar with.

Gulp是一个构建系统，开发者可以使用它在网站开发过程中自动执行常见任务。Gulp是基于Node.js构建的，因此Gulp源文件和你用来定义任务的Gulp文件都被写进了JavaScript（或者CoffeeScript）里。前端开发工程师还可以用自己熟悉的语言来编写任务去lint JavaScript和CSS、解析模板以及在文件变动时编译LESS文件（当然这些只是一小部分例子）。

The system by itself doesn’t really do much, but a huge number of plugins are available, which you can see on the plugins page or by searching gulpplugin on npm. For example, there are plugins to run JSHint, compile your CoffeeScript, run Mocha tests and even update your version number.

Gulp本身虽然不能完成很多任务，但它有大量插件可用，开发者可以访问插件页面或者在npm搜索gulpplugin就能看到。例如，有些插件可以用来执行JSHint、编译CoffeeScript，执行Mocha测试，甚至更新版本号。

Other build tools are available, such as Grunt and, more recently, Broccoli, but I believe Gulp to be superior (see the “Why Gulp?” section below). I’ve compiled a longer list of build tools written in JavaScript.

对比其他构建工具，比如Grunt，以及最近流行的Broccoli，我相信Gulp会更胜一筹（请看后面的”Why Gulp?”部分），同时我汇总了一个使用Javascript编写的构建工具清单，可供大家参考。

## Installing

Installing is pretty easy. First, install the package globally:

安装的过程非常简单,这是安装到全局的(现在不推荐)

`npm install -g gulp`

Then, install it in your project:

然后安装到你本地的项目(这里安装到开发分支)

`npm install --save-dev gulp`

## Using

Let’s create a task to minify one of our JavaScript files. Create a file named gulpfile.js. This is where you will define your Gulp tasks, which will be run using the gulp command.

Put the following in your gulpfile.js file:

现在我们创建一个Gulp任务来压缩JavaScript文件。首先创建一个名为gulpfile.js的文件，这是定义Gulp任务的地方，它可以通过gulp命令来运行，接着把下面的代码放到gulpfile.js文件里面。

```
var gulp = require('gulp'),
   uglify = require('gulp-uglify');

gulp.task('minify', function () {
   gulp.src('js/app.js')
      .pipe(uglify())
      .pipe(gulp.dest('build'))
});
```

Install gulp-uglify through npm by running 

通过npm安装gulp-uglify插件

`npm install –save-dev gulp-uglify`

and then run the task by running gulp minify. Assuming that you have a file named app.js in the js directory, a new app.js will be created in the build directory containing the minified contents of js/app.js.

最后通过运行gulp minify来执行任务。假设js目录下有个app.js文件，那么一个新的app.js将被创建在编译目录下，它包含了js/app.js的压缩内容。

What actually happened here, though?

想一想，到底发生了什么？

We’re doing a few things in our gulpfile.js file. First, we’re loading the gulp and gulp-uglify modules:

我们只在gulpfile.js里做了一点事情。首先，我们加载gulp和gulp-uglify模块：

```
var gulp = require('gulp'),
    uglify = require('gulp-uglify');
```

Then, we’re defining a task named minify, which, when run, will call the function provided as the second argument:

然后，我们定义了一个叫minify的任务，它执行时会调用函数，这个函数会作为第二个参数：

```
gulp.task('minify', function () {

});
```

Finally — and this is where it gets tricky — we’re defining what our task should actually do:

最后，也是难点所在，我们需要定义任务应该做什么：

```
gulp.src('js/app.js')
   .pipe(uglify())
   .pipe(gulp.dest('build'))
```

Unless you’re familiar with streams, which most front-end developers are not, the code above won’t mean much to you.

如果你对数据流非常熟悉（其实大多数前端开发人员并不熟悉），上面所提供的代码对你来说就没有太大意义了

## STREAMS

Streams enable you to pass some data through a number of usually small functions, which will modify the data and then pass the modified data to the next function.

数据流能够通过一系列的小函数来传递数据，这些函数会对数据进行修改，然后把修改后的数据传递给下一个函数。

In the example above, the gulp.src() function takes a string that matches a file or number of files (known as a “glob”), and creates a stream of objects representing those files. They are then passed (or piped) to the uglify() function, which takes the file objects and returns new file objects with a minified source. That output is then piped to the gulp.dest() function, which saves the changed files.

在上面的例子中，gulp.src()函数用字符串匹配一个文件或者文件的编号（被称为“glob”）,然后创建一个对象流来代表这些文件，接着传递给uglify()函数，它接受文件对象之后返回有新压缩源文件的文件对象，最后那些输出的文件被输入gulp.dest()函数，并保存下来。

When there is only one task, the function doesn’t really do much. However, consider the following code:

当只有一个任务的时候，函数并不会起太大的作用。然而，仔细思考下面的代码：

```
gulp.task('js', function () {
   return gulp.src('js/*.js')
      .pipe(jshint())
      .pipe(jshint.reporter('default'))
      .pipe(uglify())
      .pipe(concat('app.js'))
      .pipe(gulp.dest('build'));
});

```

To run this yourself, install gulp, gulp-jshint, gulp-uglify and gulp-concat.

在运行这段程序之前，你需要先安装gulp,gulp-jshint,gulp-uglify和gulp-concat。

This task will take all files matching js/*.js (i.e. all JavaScript files from the js directory), run JSHint on them and print the output, uglify each file, and then concatenate them, saving them to build/app.js. In diagram form

这个任务会让所有的文件匹配js/*.js（比如js目录下的所有JavaScript文件），并且执行JSHint，然后打印输出结果，取消文件缩进，最后把他们合并起来，保存为build/app.js，整个过程如下图所示： 

If you’re familiar with Grunt, then you’ll notice that this is pretty different to how Grunt does it. Grunt doesn’t use streams; instead, it takes files, runs a single task on them and saves them to new files, repeating the entire process for every task. This results in a lot of hits to the file system, making it slower than Gulp.

如果你对Grunt 足够熟悉，就会注意到，Gulp和Grunt的工作方式很不一样。Grunt不使用数据流，而是使用文件，对文件执行单个任务然后保存到新的文件中，每个任务都会重复执行所有进程，文件系统频繁的处理任务会导致Grunt的运行速度比Gulp慢。

For a more comprehensive read on streams, check out the “Stream Handbook.”

如果想要获取更加全面的数据流知识，请查看“Stream Handbook”.

**注意: 安装gulp-jshint 必须还要安装 jshint**

## GULP.SRC()

The gulp.src() function takes a glob (i.e. a string matching one or more files) or an array of globs and returns a stream that can be piped to plugins.

gulp.src()方法输入一个glob(比如匹配一个或多个文件的字符串)或者glob数组，然后返回一个可以传递给插件的数据流。

Gulp uses node-glob to get the files from the glob or globs you specify. It’s easiest to explain using examples:

Gulp使用node-glob来从你指定的glob里面获取文件，这里列举下面的例子来阐述，方便大家理解：

* js/app.js Matches the exact file
    精确匹配文件
* js/*.js Matches all files ending in .js in the js directory only
    仅匹配js目录下的所有后缀为.js的文件
* js/**/*.js Matches all files ending in .js in the js directory and all child directories
    匹配js目录及其子目录下所有后缀为.js的文件
* !js/app.js Excludes js/app.js from the match, which is useful if you want to match all files in a directory except for a particular file
    从匹配结果中排除js/app.js，这种方法在你想要匹配除了特殊文件之外的所有文件时非常管用
* *.+(js|css) Matches all files in the root directory ending in .js or .css
    匹配根目录下所有后缀为.js或者.css的文件

Other features are available, but they’re not commonly used in Gulp. Check out out the Minimatch documentation for more.

此外，Gulp也有很多其他的特征，但并不常用。如果你想了解更多的特征，请查看Minimatch文档。

Let’s say that we have a directory named js containing JavaScript files, some minified and some not, and we want to create a task to minify the files that aren’t already minified. To do this, we match all JavaScript files in the directory and then exclude all files ending in .min.js:

js目录下包含了压缩和未压缩的JavaScript文件，现在我们想要创建一个任务来压缩还没有被压缩的文件，我们需要先匹配目录下所有的JavaScript文件，然后排除后缀为.min.js的文件:

```
gulp.src(['js/**/*.js', '!js/**/*.min.js'])
```

## DEFINING TASKS

To define a task, use the gulp.task() function. When you define a simple task, this function takes two attributes: the task’s name and a function to run.

gulp.task()函数通常会被用来定义任务。当你定义一个简单的任务时，需要传入任务名字和执行函数两个属性。

```
gulp.task('greet', function () {
   console.log('Hello world!');
});
```

Running gulp greet will result in “Hello world” being printed to the console.

执行gulp greet的结果就是在控制台上打印出“Hello world”.

A task may also be a list of other tasks. Suppose we want to define a build task that runs three other tasks, css, js and imgs. We can do this by specifying an array of tasks, instead of the function:

一个任务有时也可以是一系列任务。假设要定义一个任务build来执行css、js、imgs这三个任务，我们可以通过指定一个任务数组而不是函数来完成。

```
gulp.task('build', ['css', 'js', 'imgs']);
```

These will be run asynchronously, so you can’t assume that the css task will have finished running by the time js starts — in fact, it probably won’t have. To make sure that a task has finished running before another task runs, you can specify dependencies by combining the array of tasks with the function. For example, to define a css task that checks that the greet task has finished running before it runs, you can do this:

这些任务不是同时进行的，所以你不能认为在js任务开始的时候css任务已经结束了，也可能还没有结束。为了确保一个任务在另一个任务执行前已经结束，可以将函数和任务数组结合起来指定其依赖关系。例如，定义一个css任务，在执行前需要检查greet任务是否已经执行完毕，这样做就是可行的:

```
gulp.task('css', ['greet'], function () {
   // Deal with CSS here
});
```

Now, when you run the css task, Gulp will execute the greet task, wait for it to finish, and then call the function that you’ve specified.

现在，当执行css任务时，Gulp会先执行greet任务，然后在它结束后再调用你定义的函数。

## DEFAULT TASKS

You can define a default task that runs when you just run gulp. You can do this by defining a task named default.

你可以定义一个在gulp开始运行时候默认执行的任务，并将这个任务命名为“default”：

```
gulp.task('default', function () {
   // Your default task
});
```

## PLUGINS

You can use a number of plugins — over 600, in fact — with Gulp. You will find them listed on the plugins page or by searching gulpplugin on npm. Some plugins are tagged “gulpfriendly”; these aren’t plugins but are designed to work well with Gulp. Be aware when searching directly on npm that you won’t be able to see whether a plugin has been blacklisted (scrolling to the bottom of the plugins page, you will see that many are).

Gulp上有超过600种插件供你选择，你可以在插件页面或者npm上搜索gulpplugin来浏览插件列表。有些拥有“gulpfriendly”标签的插件，他们不能算插件，但是能在Gulp上正常运行。 需要注意的是，当直接在npm里搜索时，你无法知道某一插件是否在黑名单上（你需要滚动到插件页面底部才能看到）。

Most plugins are pretty easy to use, have good documentation and are run in the same way (by piping a stream of file objects to it). They’ll then usually modify the files (although some, such as validators, will not) and return the new files to be passed to the next plugin.

大多数插件的使用都很方便，它们都配有详细的文档，而且调用方法也相同（通过传递文件对象流给它），它们通常会对这些文件进行修改（但是有一些插件例外，比如validators），最后返回新的文件给下一个插件。

Let’s expand on our js task from earlier:

让我们用前面的js任务来详细说明一下：

```
var gulp = require('gulp'),
    jshint = require('gulp-jshint'),
    uglify = require('gulp-uglify'),
    concat = require('gulp-concat');

gulp.task('js', function () {
   return gulp.src('js/*.js')
      .pipe(jshint())
      .pipe(jshint.reporter('default'))
      .pipe(uglify())
      .pipe(concat('app.js'))
      .pipe(gulp.dest('build'));
});

```

We’re using three plugins here, gulp-jshint, gulp-uglify and gulp-concat. You can see in the README files for the plugins that they’re pretty easy to use; options are available, but the defaults are usually good enough.

You might have noticed that the JSHint plugin is called twice. That’s because the first line runs JSHint on the files, which only attaches a jshint property to the file objects without outputting anything. You can either read the jshint property yourself or pass it to the default JSHint reporter or to another reporter, such as jshint-stylish.

这里使用了三个插件，gulp-jshint,gulp-uglify和gulp-concat。开发者可以参考插件的README文档，插件有很多配置选项，而且给定的初始值通常能满足需求。细心的读者可能会发现，程序中JSHint插件执行了2次，这是因为第一次执行JSHint只是给文件对象附加了jshint属性，并没有输出。你可以自己读取jshint的属性或者传递给默认的JSHint的接收函数或者其他的接收函数,比如jshint-stylish.

The other two plugins are clearer: the uglify() function minifies the code, and the concat(‘app.js’) function concatenates all of the files into a single file named app.js.

其他两个插件的作用很清楚：uglify()函数压缩代码，concat(‘app.js’)函数将所有文件合并到一个叫app.js的文件中。

## GULP-LOAD-PLUGINS

A module that I find really useful is gulp-load-plugins, which automatically loads any Gulp plugins from your package.json file and attaches them to an object. Its most basic usage is as follows:

我发现gulp-load-plugin模块十分有用，它能够自动地从package.json中加载任意Gulp插件然后把它们附加到一个对象上。它的基本用法如下所示：

```
var gulpLoadPlugins = require('gulp-load-plugins'),
    plugins = gulpLoadPlugins();
```

You can put that all on one line (var plugins = require(‘gulp-load-plugins’)();), but I’m not a huge fan of inline require calls.

你可以把所有代码写到一行，但是我并不推荐这样做。

After running that code, the plugins object will contain your plugins, camel-casing their names (for example, gulp-ruby-sass would be loaded to plugins.rubySass). You can then use them as if they were required normally. For example, our js task from before would be reduced to the following:

在执行那些代码之后，插件对象就已经包含了插件，并使用“驼峰式”的方式进行命名（例如，gulp-ruby-sass将被加载成plugins.rubySass），这样就可以很方便地使用了。例如，前面的js任务简化为如下：

```
var gulp = require('gulp'),
    gulpLoadPlugins = require('gulp-load-plugins'),
    plugins = gulpLoadPlugins();

gulp.task('js', function () {
   return gulp.src('js/*.js')
      .pipe(plugins.jshint())
      .pipe(plugins.jshint.reporter('default'))
      .pipe(plugins.uglify())
      .pipe(plugins.concat('app.js'))
      .pipe(gulp.dest('build'));
});
```

This assumes a package.json file that is something like the following:

假设package.json文件如下面所示：

```
{
   "devDependencies": {
      "gulp-concat": "~2.2.0",
      "gulp-uglify": "~0.2.1",
      "gulp-jshint": "~1.5.1",
      "gulp": "~3.5.6"
   }
}
```

In this example, it’s not actually much shorter. However, with longer and more complicated Gulp files, it reduces a load of includes to just one or two lines.

这个例子虽然已经够短了，但是使用更长更复杂的Gulp文件会把它们简化成一两行代码。

Version 0.4.0 of gulp-load-plugins, released in early March, added lazy plugin loading, which improves performance. Plugins are not loaded until you call them, meaning that you don’t have to worry about unused plugins in package.json affecting performance (although you should probably clean them up anyway). In other words, if you run a task that requires only two plugins, it won’t load all of the plugins that the other tasks require.

三月初发布的Gulp-load-plugins0.4.0版本添加了延迟加载功能，提高了插件的性能，因为插件在使用的时候才会被加载进来，你不用担心package.json里未被使用的插件影响性能（但是你需要把他们清理掉）。换句话说，如果你在执行任务时只需要两个插件，那么其他不相关的插件就不会被加载。

## WATCHING FILES

Gulp has the ability to watch files for changes and then run a task or tasks when changes are detected. This feature is amazingly useful (and, for me, probably Gulp’s single most useful one). You can save your LESS file, and Gulp will turn it into CSS and update the browser without your having to do anything.

Gulp可以监听文件的修改动态，然后在文件被改动的时候执行一个或多个任务。这个特性十分有用（对我来说，这可能是Gulp中最有用的一个功能）。你可以保存LESS文件，接着Gulp会自动把它转换为CSS文件并更新浏览器。

To watch a file or files, use the gulp.watch() function, which takes a glob or array of globs (the same as gulp.src()) and either an array of tasks to run or a callback.

使用gulp.watch()方法可以监听文件，它接受一个glob或者glob数组（和gulp.src()一样）以及一个任务数组来执行回调。

Let’s say that we have a build task that turns our template files into HTML, and we want to define a watch task that watches our template files for changes and runs the task to turn them into HTML. We can use the watch function as follows:

让我们看看下面，build任务可以将模板转换成html格式，然后我们希望定义一个watch任务来监听模板文件的变化，并将这些模板转换成html格式。watch函数的使用方法如下所示：

```
gulp.task('watch', function () {
   gulp.watch('templates/*.tmpl.html', ['build']);
});
```

Now, when we change a template file, the build task will run and the HTML will be generated.

现在，当改变一个模板文件时，build任务会被执行并生成HTML文件，也可以给watch函数一个回调函数，而不是一个任务数组。在这个示例中，回调函数有一个包含触发回调函数信息的event对象：

```
gulp.watch('templates/*.tmpl.html', function (event) {
   console.log('Event type: ' + event.type); // added, changed, or deleted
   console.log('Event path: ' + event.path); // The path of the modified file
});
```

You can also give the watch function a callback, instead of an array of tasks. In this case, the callback would be given an event object containing some information about the event that triggered the callback:

Gulp.watch()的另一个非常好的特性是返回我们熟知的watcher。利用watcher来监听额外的事件或者向watch中添加文件。例如，在执行一系列任务和调用一个函数时，你就可以在返回的watcher中添加监听change事件:

```
var watcher = gulp.watch('templates/*.tmpl.html', ['build']);
watcher.on('change', function (event) {
   console.log('Event type: ' + event.type); // added, changed, or deleted
   console.log('Event path: ' + event.path); // The path of the modified file
});
```

Another nifty feature of gulp.watch() is that it returns what is known as a watcher. Use the watcher to listen for additional events or to add files to the watch. For example, to both run a list of tasks and call a function, you could add a listener to the change event on the returned watcher:Another nifty feature of gulp.watch() is that it returns what is known as a watcher. Use the watcher to listen for additional events or to add files to the watch. For example, to both run a list of tasks and call a function, you could add a listener to the change event on the returned watcher:

除了change事件，还可以监听很多其他的事件:

In addition to the change event, you can listen for a number of other events:

end Fires when the watcher ends (meaning that tasks and callbacks won’t be called anymore when files are changed)
error Fires when an error occurs
ready Fires when the files have been found and are being watched
nomatch Fires when the glob doesn’t match any files
The watcher object also contains some methods that you can call:

watcher.end() Stops the watcher (so that no more tasks or callbacks will be called)
watcher.files() Returns a list of files being watched by the watcher
watcher.add(glob) Adds files to the watcher that match the specified glob (also, accepts an optional callback as a second argument)
watcher.remove(filepath) Removes a particular file from the watcher

end 在watcher结束时触发（这意味着，在文件改变的时候，任务或者回调不会执行）
error 在出现error时触发
ready 在文件被找到并正被监听时触发
nomatch 在glob没有匹配到任何文件时触发
Watcher对象也包含了一些可以调用的方法：

watcher.end() 停止watcher（以便停止执行后面的任务或者回调函数）
watcher.files() 返回watcher监听的文件列表
watcher.add(glob) 将与指定glob相匹配的文件添加到watcher（也接受可选的回调当第二个参数）
watcher.remove(filepath) 从watcher中移除个别文件

## Reloading Changes In The Browser

You can get Gulp to reload or update the browser when you — or, for that matter, anything else, such as a Gulp task — changes a file. There are two ways to do this. The first is to use the LiveReload plugin, and the second is to use BrowserSync

当一个文件被修改或者Gulp任务被执行时可以用Gulp来加载或者更新网页。LiveReload和BrowserSync插件就可以用来实现在游览器中加载更新的内容。


## LIVERELOAD

LiveReload combines with browser extensions (including a Chrome extension) to reload your browser every time a change to a file is detected. It can be used with the gulp-watch plugin or with the built-in gulp.watch() that I described earlier. Here’s an example from the README file of the gulp-livereload repository:

LiveReload结合了浏览器扩展（包括Chrome extension），在发现文件被修改时会实时更新网页。它可以和gulp-watch插件或者前面描述的gulp-watch()函数一起使用。下面有一个gulp-livereload仓库中的README文件提到的例子:

```
var gulp = require('gulp'),
    less = require('gulp-less'),
    livereload = require('gulp-livereload'),
    watch = require('gulp-watch');

gulp.task('less', function() {
   gulp.src('less/*.less')
      .pipe(watch())
      .pipe(less())
      .pipe(gulp.dest('css'))
      .pipe(livereload());
});
```

This will watch all files matching less/*.less for changes. When a change is detected, it will generate the CSS, save the files and reload the browser.

这会监听到所有与less/*.less相匹配的文件的变化。一旦监测到变化，就会生成css并保存，然后重新加载网页.

## BROWSERSYNC

An alternative to LiveReload is available. BrowserSync is similar in that it displays changes in the browser, but it has a lot more functionality.

BroserSync在浏览器中展示变化的功能与LiveReload非常相似，但是它有更多的功能。

When you make changes to code, BrowserSync either reloads the page or, if it is CSS, injects the CSS, meaning that the page doesn’t need to be refreshed. This is very useful if your website isn’t refresh-resistant. Suppose you’re developing four clicks into a single-page application, and refreshing the page would take you back to the starting page. With LiveReload, you would need to click four times every time you make a change. BrowserSync, however, would just inject the changes when you modify the CSS, so you wouldn’t need to click back.

当你改变代码的时候，BrowserSync会重新加载页面，或者如果是css文件，会直接添加进css中，页面并不需要再次刷新。这项功能在网站是禁止刷新的时候是很有用的。假设你正在开发单页应用的第4页，刷新页面就会导致你回到开始页。使用LiveReload的话，你就需要在每次改变代码之后还需要点击四次，而当你修改CSS时，插入一些变化时，BrowserSync会直接将需要修改的地方添加进CSS，就不用再点击回退。

BrowserSync also synchronizes clicks, form actions and your scroll position between browsers. You could open a couple of browsers on your desktop and another on an iPhone and then navigate the website. The links would be followed on all of them, and as you scroll down the page, the pages on all of the devices would scroll down (usually smoothly, too!). When you input text in a form, it would be entered in every window. And when you don’t want this behavior, you can turn it off.

BrowserSync也可以在不同浏览器之间同步点击翻页、表单操作、滚动位置。你可以在电脑和iPhone上打开不同的浏览器然后进行操作。所有设备上的链接将会随之变化，当你向下滚动页面时，所有设备上页面都会向下滚动（通常还很流畅！）。当你在表单中输入文本时，每个窗口都会有输入。当你不想要这种行为时，也可以把这个功能关闭。

BrowserSync doesn’t require a browser plugin because it serves your files for you (or proxies them, if they’re dynamic) and serves a script that opens a socket between the browser and the server. This hasn’t caused any problems for me in the past, though.

BrowserSync不需要使用浏览器插件，因为它本身就可以为你提供文件服务（如果文件是动态的，则为他们提供代理服务）和用来开启浏览器和服务器之间的socket的脚本服务。到目前为止这个功能的使用都十分顺畅。

There isn’t actually a plugin for Gulp because BrowserSync doesn’t manipulate files, so it wouldn’t really work as one. However, the BrowserSync module on npm can be called directly from Gulp. First, install it through npm:

实际上BrowserSync对于Gulp并不算一种插件，因为BrowserSync并不像一个插件一样操作文件。然而，npm上的BrowserSync模块能在Gulp上被直接调用。

首先，需要通过npm安装一下：

`npm install --save-dev browser-sync`

Then, the following gulpfile.js will start BrowserSync and watch some files:

然后gulpfile.js会启动BrowserSync并监听文件：

```
var gulp = require('gulp'),
    browserSync = require('browser-sync');

gulp.task('browser-sync', function () {
   var files = [
      'app/**/*.html',
      'app/assets/css/**/*.css',
      'app/assets/imgs/**/*.png',
      'app/assets/js/**/*.js'
   ];

   browserSync.init(files, {
      server: {
         baseDir: './app'
      }
   });
});
```

Running gulp browser-sync would then watch the matching files for changes and start a server that serves the files in the app directory.

执行gulp browser-sync后会监听匹配文件的变化，同时为app目录提供文件服务。

The developer of BrowserSync has written about some other things you can do in his BrowserSync + Gulp repository.

此外BrowserSync的开发者还写了很多关于BrowserSync+Gulp仓库的其他用途。

## Why Gulp?

前面提到过，Gulp是为数不多的使用JavaScript开发的构建工具之一，也有其他不是用JavaScript开发的构建工具，比如Rake，那么我们为什么要选择Gulp呢?

The two most popular build tools in JavaScript are Grunt and Gulp. Grunt was very popular in 2013 and completely changed how a lot of people develop websites. Thousands of plugins are available for it, doing everything from linting, minifying and concatenating code to installing packages using Bower and starting an Express server. This approach is pretty different from Gulp’s, which has only plugins to perform small individuals tasks that do things with files. Because tasks are just JavaScript (unlike the large object that Grunt uses), you don’t need a plugin; you can just start an Express server the normal way.

目前最流行的两种使用JavaScript开发的构建工具是Grunt和Gulp。Grunt在2013年非常流行，因为它彻底改变了许多人开发网站的方式，它有上千种插件可供用户使用，从linting、压缩、合并代码到使用Bower安装程序包,启动Express服务都能办到。这些和Gulp的很不一样，Gulp只有执行单个小任务来处理文件的插件，因为任务都是JavaScript（和Grunt使用的大型对象不同），根本不需要插件，你只需用传统方法启动一个Express服务就可以了。

Grunt tasks tend to be over-configured, requiring a large object containing properties that you really wouldn’t want to have to care about, while the same task in Gulp might take up only a few lines. Let’s look at a simple gruntfile.js that defines a css task to convert our LESS to CSS and then run Autoprefixer on it:

Grunt任务拥有大量的配置，会引用大量你实际上并不需要的对象属性，但是Gulp里同样的任务也许只有几行。让我们看个简单的Gruntfile.js，它规定一个将LESS转换为CSS的任务，然后执行Autoprefixer:

```
grunt.initConfig({
   less: {
      development: {
         files: {
            "build/tmp/app.css": "assets/app.less"
         }
      }
   },

   autoprefixer: {
      options: {
         browsers: ['last 2 version', 'ie 8', 'ie 9']
      },
      multiple_files: {
         expand: true,
         flatten: true,
         src: 'build/tmp/app.css',
         dest: 'build/'
      }
   }
});

grunt.loadNpmTasks('grunt-contrib-less');
grunt.loadNpmTasks('grunt-autoprefixer');

grunt.registerTask('css', ['less', 'autoprefixer']);
```

Compare this to the gulpfile.js file that does the same:

```
var gulp = require('gulp'),
   less = require('gulp-less'),
   autoprefix = require('gulp-autoprefixer');

gulp.task('css', function () {
   gulp.src('assets/app.less')
      .pipe(less())
      .pipe(autoprefix('last 2 version', 'ie 8', 'ie 9'))
      .pipe(gulp.dest('build'));
});
```

Because Grunt hits the file system far more often than Gulp, which uses streams, it is nearly always much faster than Grunt. For a small LESS file, the gulpfile.js file above would usually take about 6 milliseconds. The gruntfile.js would usually take about 50 milliseconds — more than eight times longer. This is a tiny example, but with longer files, the amount of time increases significantly.

因为Grunt比Gulp更加频繁地操作文件系统，所以使用数据流的Gulp总是比Grunt快。对于一个小的LESS文件，gulpfile.js通常需要6ms，而gruntfile.js则需要大概50ms——慢8倍多。这只是个简单的例子，对于长的文件，这个数字会增加得更显著。

## 实例代码

```
var gulp = require('gulp')
   uglify = require('gulp-uglify'),
   jshint = require('gulp-jshint'),
   concat = require('gulp-concat'),
   gulpLoadPlugins = require('gulp-load-plugins'),
   plugins = gulpLoadPlugins();

// 编写hello
gulp.task('hello',() => {
   console.log('hello gulp')
}) 
// 使用uglify插件压缩文件
gulp.task('minify', () => {
   gulp.src('js/app.js')
      .pipe(uglify())
      .pipe(gulp.dest('build'))
});
// 结合jshint插件来检测代码规范
gulp.task('jshint', () => {
   return gulp.src('js/*.js')
      .pipe(jshint())
      .pipe(jshint.reporter('default'))
      .pipe(uglify())
      .pipe(concat('app.js'))
      .pipe(gulp.dest('build'));
});
// 使用gulpLoadPlugins插件简化
gulp.task('js', () => {
   return gulp.src('js/*.js')
      .pipe(plugins.jshint())
      .pipe(plugins.jshint.reporter('default'))
      .pipe(plugins.uglify())
      .pipe(plugins.concat('app.js'))
      .pipe(gulp.dest('build'));
});
```