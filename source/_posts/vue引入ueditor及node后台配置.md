---
title: vue引入ueditor及node后台配置
date: 2017-11-13 21:24:40
tags: FE
---
最近公司的某个客户要用上我们公司的产品，他的后台管理里的富文本编辑器要求有点多，开始打算用Quill，但是发现Quill根本满足不了他的需求。在调研了市面上的富文本编辑器后，最后似乎只剩了百度的ueditor。虽然很丑~~~，不过没关系，政府网站和这种效果更搭:-D 我是不是说了什么（逃
## vue引入ueditor
### 步骤
1. 百度ueditor下载，随便哪个版本就好(本文章以php版为例)，不需要特别全面功能的可以考虑下UM喽
2. 将对应的文件夹放到static中
3. 修改前端vue部分引用的ueditor.confg.js，设置路径window.UEDITOR_HOME_URL = "/static/utf8-php/"
```javascript
window.UEDITOR_HOME_URL = "/static/utf8-php/"
    var URL = window.UEDITOR_HOME_URL || getUEBasePath();
    /**
     * 配置项主体。注意，此处所有涉及到路径的配置别遗漏URL变量。
     */
    window.UEDITOR_CONFIG = {

        //为编辑器实例添加一个路径，这个不能被注释
        UEDITOR_HOME_URL: URL
        // 服务器统一请求接口路径
        , serverUrl: "http://localhost:3000/ueditor/ue"
    // ............   下面忽略................
```
4. 编写vue文件,我已经把static配置到webpack的路径里了，自己可以相应更改，ueditor中的各项方法可以在自己下载的百度ueditor包的index.html中找。
```html
<template>
  <div class="hello">
    <script id="editor" type="text/plain"></script>
    <button @click="show">你敢点一下吗？</button>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      editor: null
    }
  },
  methods: {
    show () {
      console.log(this.editor.getContent())
    }
  },
  mounted () {
    require('static/utf8-php/ueditor.config.js')
    require('static/utf8-php/ueditor.all.min.js')
    require('static/utf8-php/lang/zh-cn/zh-cn.js')
    require('static/utf8-php/ueditor.parse.min.js')
    this.editor = window.UE.getEditor('editor')
  },
  destroyed () {
    this.editor.destroy()
  }
}
</script>

```
### 注意事项
1. 在步骤3中的路径一定要有最后一个"/"
2. 步骤3中的serverUrl写成对应的服务端地址

## node后端处理
### express 实现
网上有人已经实现了express版的，使用express的有福了。不过我直接用他的是不能直接用的，在浏览器中报": unexcepected "，我将他的代码改了一下，不让它在返回配置是重定向，而是直接返回一个jsonp，jsonp内容设置为百度的ueditor包中的php文件下的config.json，记得用正则表达式或者直接用手把注释去掉，json是没有注释的。
这时你可能发现不报错了，但是图片上传会错误，报404。其实图片已经上传成功了，只是没有正确的加载，因为返回的路径只是路径，不是完整的url，就会请求到前端服务域下。（例如http://localhost:8080/**)。此时修改config.json中"imageUrlPrefix": "http://localhost:3000",就可以将图片路径补充完整。跨域问题自己解决哈-----
1. res.jsonp(config.json)
2. 给config.json的imageUrlPrefix加后端域
### koa实现
这时个比较精巧的库，而且将在v3中去掉了generator写法，generator现在已经渐渐不被支持，所以使用async写法吧。我主要用了await-busboy这个库，实现文件处理。
1. 实现判断
```javascript
const ActionType = ctx.query.action
// 当ActionType为config时返回与express中一样的json
// 当为uploadimage或uploadfile时处理
```
2. 处理上传
```javascript
const parse = require('await-busboy')
const parts = parse(ctx)
    let part,
        stream,
        tmp_name,
        file_path,
        filename
    while ((part = await parts)) {
      if (part.length) {
          // 此处解析到form的fields
          console.log({ key: part[0], value: part[1] })
      } else {
        // 此处解析到文件并以可读流形式返回，通过nodejs官方API存储
        if(ActionType === 'uploadimage' && img_type.indexOf(path.extname(part.filename)) >= 0 ){
            filename = 'pic_'+ (new Date()).getTime() + '_' + part.filename
            file_path = path.join(img_path, filename)
        } else if (ActionType === 'uploadfile'){
            filename = 'file_'+(new Date()).getTime()+'_'+part.filename
            file_path = path.join(files_path, filename)
        }
        stream = fs.createWriteStream(path.join(static_path,file_path))
        part.pipe(stream)
        tmp_name = part.filename
    }
    // 返回json也要返回对应的jsonp形式哦~~
```
到这大概可以了，自己去试一下吧~~
