## 电商应用开发产常见技术问题FAQ

> 本人有幸参与过三个电商应用的开发，在开发的过程中遇到较多的问题，有些问题是因为自身的技术能力不够，有些是因为自己的眼界不够造成的，其实电商应用进过了这么长时间的发展，已经有很多的坑被填过了，市面上有很多的成熟的解决方案，更多的时候，我们是要根据自己的业务特性来决定采用什么样的技术，下面是我在电商应用的开发中总结的一些常见技术问题。

#### 数据库设计

在项目的前期我们会使用诸如powerdesigner之类的数据库表设计工具来设计表，注释性的信息都会被写在表设计工具生成的文件当中，在项目组成员进行开发的时候，每个人都需要获取最新的表设计工具文件，如果我们在数据库中直接可以查看到相关的字表及表注释是不是更好，所以建议可以将字段及表的注释信息可以写在数据库当中，其实表结构设计工具同样也是提供相关的功能的，可参考下面的表生成SQL:

````sql
CREATE TABLE `t_log_client_error` (
  `logid` varchar(20) NOT NULL comment '日志编号',
  `message` VARCHAR(50) NULL comment '报错信息',
  `stack` text NULL comment '报错详情',
  `openid` VARCHAR(50) null comment 'openid',
  `logtime` VARCHAR(20) NULL comment '上报时间',
  `isread` BOOL NULL comment '是否已阅读',
  PRIMARY KEY (`logid`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 comment '客户端监听日志表'
````

由于生产环境及测试环境直接的切换，我们有时候会需要将测试环境的表结构同步到生产环境，使用Navicat的数据同步工具，我们有时候会发现有些数据是无法同步过去的，这是因为某些表不存在主键，所以在设计表的时候我们需要设计主键。

电商应用有时会使用社交账号，随着时代的发展，很多的社交账户的用户名使用的是emoji字符，MySQL旧版本是不支持emoji字符的，如果想要能够存储emoji字符，需要升级MySQL到5.5.3或更高的版本，字符集需要设置为utf8mb4

2. 客户端技术选型

电商应用往往会涉及到多客户端，最主要的几大客户端为：ios、android、web、wap,wap端还可能需要细分一个微信端，对于web端，

3. 服务端热更新及负载均衡方案

4. 项目文件服务器处理

5. 规则引擎的使用

6. 单页应用优化

7. 多线程技术使用

8. 集成系统日志

9. mybatis关联查询优化

#### 客户端及服务端缓存

首先说我们遇到的问题，在我做过的一个项目当中，首页加载会调用大量的接口，大概有4个接口的样子，但是首页的内容在大部分的情况下，内容是不会改变的，如果用户频繁的回到首页，那么后台就会频繁的调用到这四个接口，如果在服务器资源不是很富裕的情况下，这显然也是一种资源的浪费。

解决的法案从大的方向上来说可以分为2种，一个是从客户端解决，还有一个是从服务端解决，先说客户端解决的思路，客户端解决，那就需要用到客户端的一些特性，在安卓端本身自带了sqllite这样的数据库，在浏览器端有cookie、localstorage、indexDB，我们可以将一些不会经常改变的数据缓存在客户端，而不需要每次都请求服务端。

我在日常的开发中使用了fetch API，使用的是dva框架，在框架中已经集成了fetch的工具方法，我们看到其原先的代码是这样的：

````javascript
import fetch from 'dva/fetch';
//返回的参数为response对象
function parseJSON(response) {
  return response.json();
}

function checkStatus(response) {
  if (response.status >= 200 && response.status < 300) {
    return response;
  }

  const error = new Error(response.statusText);
  error.response = response;
  throw error;
}
export default function request(url, options) {
  return fetch(url, options)
    .then(checkStatus)
    .then(parseJSON)
    .then((data) => ({ data }))
    .catch((err) => ({ err }));
}
````

下面我们在请求中添加客户端缓存的功能：

````javascript
import fetch from 'dva/fetch';
//返回的参数为response对象
function parseJSON(response) {
  return response.json();
}

function checkStatus(response) {
  if (response.status >= 200 && response.status < 300) {
    return response;
  }
  // 这边可以调用一个借口调用报错的接口
  const error = new Error(response.statusText);
  error.response = response;
  throw error;
}

const cachedrequest = (url, options) => {
  let expiry = 1 * 60 // 默认 1 min
  if (typeof options === 'number') {
    expiry = options
    options = undefined
  } else if (typeof options === 'object') {
    // 但愿你别设置为 0
    expiry = options.seconds || expiry
  }
  // 将 URL 作为 localStorage 的 key
  let cacheKey = url
  let cached = localStorage.getItem(cacheKey)
  let whenCached = localStorage.getItem(cacheKey + ':ts')
  if (cached !== null && whenCached !== null) {
    // 耶！ 它在 localStorage 中
    // 尽管 'whenCached' 是字符串
    // 但减号运算符会将其转换为数字
    let age = (Date.now() - whenCached) / 1000
    if (age < expiry) {
      let response = new Response(new Blob([cached]))
      return Promise.resolve(response).then(parseJSON).then((data) => ({data}))
    } else {
      // 清除旧值
      localStorage.removeItem(cacheKey)
      localStorage.removeItem(cacheKey + ':ts')
    }
  }
  return fetch(url, options).then(response => {
    // 仅在结果为 JSON 或其他非二进制数据情况下缓存结果
    if (response.status === 200) {
      let ct = response.headers.get('Content-Type')
      if (ct && (ct.match(/application\/json/i) || ct.match(/text\//i))) {
        // 当然，除了 .text()，也有 .json() 方法
        // 不过结果最终还是会以字符串形式存在 sessionStorage 中
        // 如果不克隆 response，在其返回时就会被使用
        // 这里用这种方式，保持非入侵性
        response.clone().text().then(content => {
          localStorage.setItem(cacheKey, content)
          localStorage.setItem(cacheKey+':ts', Date.now())
        })
      }
    }
    return Promise.resolve(response).then(parseJSON).then((data) => ({data}))
  })
}

export default cachedrequest
````

上面这段代码的作用就在于，每次向服务端发送请求前，该方法会在浏览器的localstorage中查找在之前是否有该接口返回数据存在，如果有，则比较该结果有没有超过失效时间，如果没有超过失效时间，则直接取localstorage中的数据，超过失效时间则会向服务端请求该接口的内容，通过该方法实现了避免多次调用内容不会改变的接口。

客户的思路大致就是这样的，接下来是服务端的缓存思路。



11. 接口对接总结
