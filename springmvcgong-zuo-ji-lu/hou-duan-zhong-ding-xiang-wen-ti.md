## 后端重定向问题

- 先说结论

ajax请求是无法重定向的，如果你需要重定向，你可以通过下面的方式请求接口：

1、a链接标签请求；
2、location.href=""请求；
3、直接在浏览器地址栏输入。

- ajax请求重定向，通过ajax请求接口重定向，你可以看到请求是成功的，能在response中看到返回的内容为重定向后额html页面，但是页面死活就是不会刷新，正常的啊，ajax请求返回的结果又不会直接显示在页面上。

4、可以将重定向的页面url返回到前台，通过前台重定向。