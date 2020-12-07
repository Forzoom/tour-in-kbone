### 在kbone中使用自定义组件

先说结论：按照下文内容继续配置并不能最终实现效果，即使用`weui-miniprogram`中的`slideview`，但仍旧记录下来，希望能对人有所帮助。

kbone中提供了[自定义组件](url 'https://wechat-miniprogram.github.io/kbone/docs/guide/advanced.html#使用小程序自定义组件')相关的文档，根据文档能够实现由开发者自己实现的自定义组件。
另外微信官方也提供了weui相关的扩展组件[weui-miniprogram](url 'https://developers.weixin.qq.com/miniprogram/dev/extended/weui/quickstart.html')，功能比较丰富。
在工作中，遇到了和`weui-miniprogram`中的`slideview`组件相似的需求，便开始尝试在`kbone`中是否能正常使用`weui-miniprogram`.

#### 原生写法中使用weui-miniprogram

在原生中可以配置`useExtendedLib`的方式导入`weui-miniprogram`，使用这种方式引入的库的代码大小不计入代码包之中。另外一种方式是通过`npm install weui-miniprogram`。

导入完成后，需要在app中进行配置，具体配置示例如下：

```javascript
{
    "usingComponents": {
        "mp-dialog": "weui-miniprogram/dialog/dialog"
    }
}
```

完成配置后，就可以在页面中直接使用。

#### 在kbone中使用自定义组件

和原生中一样写好自定义组件的代码内容后，需要对`mp-webpack-plugin`进行配置，如果使用kbone-cli创建的项目，那么就是在`build/miniprogram.config.js`中进行配置。因为这部分内容没有相关文档，所以大部分内容都是推测。

最开始时尝试直接使用`useExtendedLib`方式引入，并且按照`weui-miniprogram`文档中的方式引入，运行后失败。最终生成了`<custom-component>`的元素，但并没有真正的`slideview`组件。

```javascript
// build/miniprogram.config.js
{
    generate: {
        autoBuildNpm: "npm",
        wxCustomComponent: {
            usingComponents: {
                'mp-slideview': "weui-miniprogram/slideview/slideview",
            },
        },
    },
}
```

猜测如果一定需要使用`root`和`usingComponents`搭配才可以，即一定需要提供`slideview`代码的完整地址，因此，将引入方式改为`npm install weui-miniprogram`。使用这种方式配合`root`引入存在一个缺点，即`kbone`，会将`root`路径下的所有组件复制到项目`dist`目录下，会大大增加代码包的大小。

```javascript
// build/miniprogram.config.js
{
    generate: {
        autoBuildNpm: "npm",
        wxCustomComponent: {
            root: path.join(__dirname, '../node_modules/weui-miniprogram/miniprogram_dist'),
            usingComponents: {
                'mp-slideview': 'weui-miniprogram/slideview/slideview',
            },
        },
    },
}
```

修改后确实确实能够正常渲染`slideview`组件，但似乎组件没有正确对`touchstart`和`touchmove`事件作出响应，导致没有效果。

也需要之后能够通过调试`slideview.wxs`代码正确实现，但引入这么多不会使用的组件确实让强迫症有点难受，之后可能会选择使用`vue组件`的方式来实现类似的效果。
