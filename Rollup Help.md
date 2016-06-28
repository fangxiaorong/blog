Rollup只有一个简单的方法，rollup：

``` javascript
var rollup = require( 'rollup' );

rollup.rollup({
  // The bundle's starting point. This file will be
  // included, along with the minimum necessary code
  // from its dependencies
  entry: 'main.js'
}).then( function ( bundle ) {
  // Generate bundle + sourcemap
  var result = bundle.generate({
    // output format - 'amd', 'cjs', 'es6', 'iife', 'umd'
    format: 'cjs'
  });

  fs.writeFileSync( 'bundle.js', result.code );

  // Alternatively, let Rollup do it for you
  // (this returns a promise). This is much
  // easier if you're generating a sourcemap
  bundle.write({
    format: 'cjs',
    dest: 'bundle.js'
  });
});
```

### rollup.rollup(options)
---

返回带有bundle参数的Promise对象。支持下面这些参数（只有entry是必要的）：

#### entry

String *required* 包入口（例如你的main.js或app.js或index.js)

external
可以是
带id参数的函数，返回true为external或false非external，或
字符串数组，