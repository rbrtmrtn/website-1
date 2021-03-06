# 📝 资源类型

正如 [资源文档](assets.html) 里描述的，Parcel 将输入的文件看作 `资源（Asset）`。资源类型被看作继承自基准 `Asset`类的子类，并实现了必须的接口，去解析、分析依赖、转换及生成代码。

因为 Parcel 在多处理器内核中并行处理资源，因此资源类型所能够实施的转换行为，会被限制为那些可以在单一时间内操作单一文件的转换行为。而那些需要操作多个文件的转换行为，则需要一个自定义的[Packager](packagers.html)。

## 资源接口

```javascript
const {Asset} = require('parcel-bundler');

class MyAsset extends Asset {
  type = 'foo'; // 设置主要输出类型

  parse(code) {
    // 将代码解析为AST树
    return ast;
  }

  pretransform() {
    // 可选。在收集依赖之前转换。
  }

  collectDependencies() {
    // 分析依赖
    this.addDependency('my-dep');
  }

  transform() {
    // 可选。在收集依赖之后转换。
  }

  generate() {
    // 生成做对。如有需要，可返回多个转换(renditions)。
    // 结果会传到合适的 packagers 去生成最终的文件束
    return {
      foo: 'my stuff here', // 主输出
      js: 'some javascript' // 如若需要，此转换内容可被放到 JS 文件束中
    };
  }
}
```

## 注册资源类型

你可以用 `addAssetType` 方法在打包工具中去注册你的资源类型。它接受一个文件扩展名，以及资源类型模块的路径。它是一个路径，而非实际的对象，这样可以使它被传至 worker 进程中。

```javascript
const Bundler = require('parcel-bundler');

let bundler = new Bundler('input.js');
bundler.addAssetType('.ext', require.resolve('./MyAsset'));
```
