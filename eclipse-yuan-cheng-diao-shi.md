## eclipse 远程调试

进行过微信项目的开发工作后，会发现微信项目在开发中遇到的最多的问题就是调试问题，不管是前端还是后端，前端虽然有微信提供的开发调试工具，但是也不见得有多好用，而且吐槽下，微信的iOS端和安卓端也存在较多的需要兼容的问题。

前端的调试可以使用[eruda](https://github.com/liriliri/eruda)这款工具进行调试，当然很多的时候还是多用用微信自己的开发调试工具。

在开发微信后台的时候，遇到了支付回调的开发，本地模拟起来很是不方便，所以想到了eclipse能不能进行远程调试，百度了一下是可以的。

接下来说一下具体的方法：

我就只介绍一种，因为一般远程调试都是为了生产环境进行调试，所以介绍一下在生产环境常用的方式，首先要如果你的生产换进如果是Linux是话，那么需要修改 tomcat>bin>catalina.sh 文件，需要在文件内容的正文开始添加一段代码，注意是正文开始：

```
CATALINA_OPTS="-Xdebug  -Xrunjdwp:transport=dt_socket,address=9988,server=y,suspend=n"
```

注意一下，这里的9988就是你接下来要远程调试所用的端口，选个你喜欢的吧！

代码设置完了之后，需要配置一下本地的eclipse：

选择你要调试的项目（主要一下你的生产环境的代码，需要和你本机eclipse中的代码保持一致），有机选择 Debug As,选择 Debug Configurations :

![](/img/eclipse/debug.png)

接下来的事情就和在本地调试代码是一样的啦！退出时点击中断即可。
