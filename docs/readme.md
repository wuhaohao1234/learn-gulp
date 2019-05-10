# gulp

## 初始化

`yarn init -y`

`yarn add gulp`

创建gulpfile.js

```
function defaultTask(cb) {
    console.log('hello gulp')
    cb();   
}

exports.default = defaultTask
```

## 创建任务

### 出口

任务可以被视为公共或私人

* 公共任务从gulpfile导出，允许它们由gulp命令运行。

* 私人任务在内部使用，通常用作部分series()或parallel()组合。

私有任务看起来像任何其他任务一样，但最终用户不能独立执行它。要公开注册任务，请从gulpfile中导出它。

```
const { series } = require('gulp');

// The `clean` function is not exported so it can be considered a private task.
// It can still be used within the `series()` composition.
function clean(cb) {
  // body omitted
  cb();
}

// The `build` function is exported so it is public and can be run with the `gulp` command.
// It can also be used within the `series()` composition.
function build(cb) {
  // body omitted
  cb();
}

exports.build = build;
exports.default = series(clean, build);
```

过去，task()用于将您的功能注册为任务。虽然该API仍然可用，但导出应该是主要的注册机制，除非在导出不起作用的边缘情况下。

### 撰写任务

Gulp提供了两种组合物强大的方法，series()以及parallel()，允许单独的任务可以由成更大的操作。两种方法都接受任意数量的任务函数或组合操作。series()并且parallel()可以嵌套在自身内或彼此嵌套到任何深度。

要按顺序执行任务，请使用该series()方法。

```
const { series } = require('gulp');

function transpile(cb) {
  // body omitted
  cb();
}

function bundle(cb) {
  // body omitted
  cb();
}

exports.build = series(transpile, bundle);
```

对于以最大并发性运行的任务，请将它们与parallel()方法结合使用。

```
const { parallel } = require('gulp');

function javascript(cb) {
  // body omitted
  cb();
}

function css(cb) {
  // body omitted
  cb();
}

exports.build = parallel(javascript, css);
```

任何一个series()或被parallel()调用时，任务立即组成。这允许组成的变化而不是单个任务内的条件行为

```
const { series } = require('gulp');

function minify(cb) {
  // body omitted
  cb();
}


function transpile(cb) {
  // body omitted
  cb();
}

function livereload(cb) {
  // body omitted
  cb();
}

if (process.env.NODE_ENV === 'production') {
  exports.build = series(transpile, minify);
} else {
  exports.build = series(transpile, livereload);
}
```

series()并且parallel()可以嵌套到任意深度。

```
const { series, parallel } = require('gulp');

function clean(cb) {
  // body omitted
  cb();
}

function cssTranspile(cb) {
  // body omitted
  cb();
}

function cssMinify(cb) {
  // body omitted
  cb();
}

function jsTranspile(cb) {
  // body omitted
  cb();
}

function jsBundle(cb) {
  // body omitted
  cb();
}

function jsMinify(cb) {
  // body omitted
  cb();
}

function publish(cb) {
  // body omitted
  cb();
}

exports.build = series(
  clean,
  parallel(
    cssTranspile,
    series(jsTranspile, jsBundle)
  ),
  parallel(cssMinify, jsMinify),
  publish
);
```

运行组合操作时，每次引用时都会执行每个任务。例如，clean在两个不同任务之前引用的任务将运行两次并导致不希望的结果。相反，重构clean要在最终组合中指定的任务。

如果您有这样的代码：

```

// This is INCORRECT
const { series, parallel } = require('gulp');

const clean = function(cb) {
  // body omitted
  cb();
};

const css = series(clean, function(cb) {
  // body omitted
  cb();
});

const javascript = series(clean, function(cb) {
  // body omitted
  cb();
});

exports.build = parallel(css, javascript);
```

迁移到：

```
const { series, parallel } = require('gulp');

function clean(cb) {
  // body omitted
  cb();
}

function css(cb) {
  // body omitted
  cb();
}

function javascript(cb) {
  // body omitted
  cb();
}

exports.build = series(clean, parallel(css, javascript));
```

