title: yct
---
yct 111111 ru ru ru

### deployBefore

在部署完成前发布。

### deployAfter

在部署成功后发布。

### exit

在 Hexo 结束前发布。

### generateBefore

在静态文件生成前发布。

### generateAfter

在静态文件生成后发布。

### new

在文章文件建立后发布。该事件返回文章参数。

``` js
hexo.on('new', function(post){
  // 
});
```

资料 | 描述
--- | ---
`post.path` | 文章文件的完整路径
`post.content` | 文章文件的内容

### processBefore

在处理原始文件前发布。此事件会返回一个地址，代表 Box（Box）的根目录。

### processAfter

在原始文件处理后发布。此事件会返回一个地址，代表 Box（Box）的根目录。

### ready

在初始化完成后发布。

[EventEmitter]: http://nodejs.org/api/events.html