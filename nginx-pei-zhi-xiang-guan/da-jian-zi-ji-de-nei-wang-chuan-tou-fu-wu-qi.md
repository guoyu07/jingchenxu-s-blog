## 搭建自己的内网穿透服务器

> 首先[github](https://github.com/jingchenxu)是个好东西，开始有越来越多的人开始利用github的，并投入到代码开源的伟大事业中来，好的我们回正题，我们为什么需要内网穿透服务，至于为什么，只要又一次微信开发经历，相信很快就会明白内网穿透是多么令人愉快的事情了。

- 选择何种软件进行内网穿透

如果有钱可以选择简单直接的方式，使用花生壳来进行内网穿透，但是效果不是很好，买的便宜的网速也不是很好，在调试的时候能急死人，买贵的如果公司能报销那也不是什么问题，只要直接开搞就可以了。

如果自己有个服务器并且还有一个备案的域名的话，那可以考虑通过开源软件自己搭建一个内网穿透了，一开始通过百度只发现了一款，golang开发，貌似还是国人写的，但是项目不是很友好，作者的2.0版本闭源了，目前已经作为商业产品在卖钱了，它就是[ngrok](https://github.com/inconshreveable/ngrok),有兴趣的人可以折腾一下，貌似还是可用的，我折腾了半天，没弄好，所以就转投另一个开源产品了[frp](https://github.com/fatedier/frp),同样也是golang开发的，也是国人开发的，但是文档相对来说比较友好，所以使用就比较方便了，好吧自己看文档吧！