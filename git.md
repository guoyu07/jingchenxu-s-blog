## git修改ssh 为 http

公司的gitlab又出问题了，通过ssh不能访问项目了，百度了一下，可能是端口被封了，看到同事的git项目还能继续访问，原来他们用的是http方式访问的，修改方式很简单，将.git 文件夹中的 config 文件中的 url 修改为http方式对应的url。