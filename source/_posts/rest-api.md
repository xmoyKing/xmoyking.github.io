---
title: 如何设计友好的REST API
categories:
  - mixed
tags:
  - rest
date: 2017-09-14 21:59:04
updated:
---

#### URI

在REST中的典型uri一般是由名称和id组成，如：
- /users 表示所有用户列表
- /users/1 表示id为1的用户

若无特殊要求，id最好使用uuid，即使在非REST的架构中，uuid也有使用简单、无锁的优势。而在REST的架构中，除了上面的优点，还有利于分布式系统中独立处理，可以各自插入数据，而不用担心id冲突。

除了单层资源，在多层资源上，如：
- /users/1/files 表示用户1的所有文件
- /users/1/files/2 表示用户1的文件2

注意：资源层次不是越深约好的，参数过多会有使用不便的问题

理论上，任何多层资源都是由多个两层资源组合二层，所以本质上资源嵌套只要两层就够了，特别是使用uuid的情况下，可以更安全的拆分成两级嵌套，因此，除非在表意上确实有必要，否则使用两层资源即可。

定义uri的一个误区使表一些查询参数直接定义在uri中，如/users/age/20/60，显然比/users?minAge=20&&maxAge=60要难看多了。

#### 资源拆分
资源与领域类具有相当高的对应关系，资源的关联结构和领域类的关联结构也具有很大的相似性，当UML图出来的时候，REST API大概也出来了。

对于UML中表现为常规关联（Association，即普通箭头）或聚合（Aggregation，即空心方块）的关联，通常不需要设计多层资源，而组合（Composition,即实心方块）则意味着多层资源。

#### 资源命名
如类、变量、函数的命名一样，资源命名也是一门学问。

首先，资源名称应该是名称，也包括动名词的形式，但是不应该是动词。总之，资源要作为能够操作的对象，传统RPC形式的一些API也要映射为对资源的操作，比如登录过程其实就是一个创建安全令牌（Token）的过程，可以将API设计为
`POST /tokens`, 注销则是`DELETE /tokens/:id`

其次，需要注意名称的表意性，要尽量恰如其分的表示其业务含义，由于API的生命周期通常很长，所以多花精力是值得的。恰如其分就是只要在当前语境下用词贴切即可，不可过于啰嗦，如files不应该命名为userFiles

最后，要避免使用缩写词，避免使用有歧义的缩写甚至自创缩写，但像org这样的众所周知的缩写还是可以使用的。判断命名是否合适，最简单的就是找一个了解业务背景的非技术人员，是否能一眼看懂。

#### 方法
除了URI之外，还要遵循METHOD（方法）的使用规范，这个规范不是REST定义的，而是HTTP的规范。METHOD中有两个概念，一个是安全性，一个是幂等性。
1. 安全性
  HTTP METHOD的安全性其实与传统的“安全”不是一个概念，而是指一项操作不会改变资源的状态，即，该操作对资源是只读的。根据HTTP规范，GET和HEAD是安全的，而POST、PUT、DELETE是不安全的，除此之外、常用的自定义方法PATCH也是不安全的。

  这里所说的是“不改变资源的状态”，而不是指不能在服务端做任何改变，比如，在GET方法中记录日志是不违反规范的。遵循安全性的要求来实现方法有利于充分利用互联网的各级缓存设施，无论是在反向代理一级、代理服务器一级还是浏览器一级，都默认对“安全”的请求进行缓存，而会改变状态的操作默认不能缓存，否则容易破坏业务逻辑。

  虽然“安全”不是同一概念，但HTTP方法的安全与“攻击”概念下的安全也有一定联系。
  
  若GET方法的实现会改变资源状态，比如，允许通过GET请求投票，那么只要有人点击或者请求这个链接，就会自动“投票”，这就是不安全的了。

  还有很多攻击方式，但总体来说，针对GET攻击的手段要比其他METHOD的多一点，所以，让GET实现不要改变资源状态，是一个极容易实现的原则，值得遵循。

2. 幂等性
  这里的“幂”与数学的“幂”是一个概念。在数学上，x的n次幂表示把x连乘n次，而操作的幂是指把同一个操作连续执行n次。所谓“幂等”，就是指n取任何值其结果都与n取1相等。

  操作上的幂等也是这样的概念，比如GET是安全的所以GET不会改变操作的状态，无论GET多少次都不会改变资源的结果，所以GET也是幂等的。而POST会创建一个新的资源，若再次执行同一个代码又会创建一个新的相同的内容，这就不是幂等的。
  
  而DELETE和PUT操作是不安全的，但确实幂等的，比如DELETE操作是删除指定ID的资源，而结果就是该ID的资源被删除导致不存在，再执行一次，资源仍然不存在，所以结果是相同的，所以是幂等的。PUT的原理类似，因为用相同的数据去更新一个资源，其结果相同，无论执行多少次。

  幂等的意义在于，它可以安全的自动重试刚才的操作，而不用担心破坏业务逻辑。比如浏览器的页面可以自由刷新，是幂等的，而提交表单却需要提示是否重试，这就是因为POST操作不是幂等的，自动重试将导致预期之外的结果。

关于安全性和幂等性总结为如下的表：

 | 方法 | 含义 | 安全 | 幂等 |
 | - | - |:-:|:-:|
 | GET | 读取资源列表或指定资源详情 | 是 | 是 |
 | HEAD | 获取资源概况 | 是 | 是 |
 | POST | 创建新资源 | 否 | 否 |
 | PUT | 更新指定资源 | 否 | 是 |
 | DELETE | 创建新资源 | 否 | 是 |

#### 返回值
与RPC风格不同，在REST风格中，成功、失败等错误码应该在响应（response）头中作为HTTP status code返回，比如200, 401等，而不能在响应体中返回，这是HTTP规范的要求，也是REST规范所要求的。

在编程时，统一具有很高的实用性。比如在前端写一个过滤层，对服务端的错误信息进行统一处理，在ng中，这种过滤层被成为http interceptor，它可以对每一个发出的包、收到的包进行处理，包括解析内容，修改内容等。

返回码的值需要遵循HTTP规范中的含义，常用如下：

 | 状态码 | 含义 | 用法 |
 | - | - | - | 
 | 200 | 成功 | 当读取（GET）或更新（PUT）成功时返回 | 
 | 201 | 已创建 | 当创建（POST）成功返回 | 
 | 202 | 已接受 | 当接受了一个资源，但尚未写入完成时返回，可用于异步处理 | 
 | 204 | 没有内容 | 可以用来表示删除（DELETE）成功 | 
 | ... | ... | ... | 

更多更具体可以查看[HTTP状态码对照表 大全](http://tools.jb51.net/table/http_status_code)


除此之外，1xx和3xx系列的状态码表示临时状态，是留给Web服务器层使用的，应用程序不应该主动返回它，前端程序也不需要处理它，除了500和501之外的5xx系列状态码只要保留给各种网关，一般也用不到，若需要使用，则需要到RFC中详细研究含义。

自定义错误码一般应该放在4xx区（失败类）和2xx区（成功类），除非确定无法找到合适的标准码，否则不要自定义状态码，因为自定义状态码需要学习成本，容易让接口丧失通用和简单性。若确定需要自定义，则需要通过充分的沟通同时做好文档工作。

#### 分页API案例
以`GET /users`为例分析一个分页的API设计。

1. URI的设计：
  当分页时，分页信息其实是查询参数，不应该成为URI的一部分，应该应该使用`GET /users?分页参数`
2. 分页参数的设计：
  有两种方案，一个是“页大小+页号”，一个是“起始位置+条数”。那种更好呢？推荐是后者，因为页面在本质上是前端的概念，前端可以按照每次100项向后端请求数据，但是展示给用户的时候用10项/页展示。若在API中规定了页号，那么使用起来容易导致困惑。甚至，有的时候前端不分页而是采用滚动加载，这种模式的页数就有些别扭了。
  而“起始位置+条数”的模式则适应性更好，在两种该练下都很容易理解，同时，与数据库中的分页也是相同的概念，所以可以直接传给数据库查询。
3. 命名，尽量统一：
  建议使用offset和size，条数避免叫length，因为它和数组的长度，字符串长度命名相同，同时容易干扰其他代码或第三方库。
4. 返回值：
  状态码要遵循前述的规范，注：列表中没有数据是状态码为200，并返回空数组，而有数据时返回一个符合条件的数据列表。ng的$resource.query函数所期待的响应体也是一个数组。当返回的时一个数组而不是对象时，总条数应该放哪儿呢？ 答案是：在响应头中，在响应头中添加一个自定义头，比如X-Record-Count，用来表示符合条件的总条数。
5. 关于METHOD：
  在SPA模式下，总数可以保存在前端，那么也就意味着没有必要为每个请求都重新请求一次总数，即只需要在页面初始化调用一次即可，使用`HEAD /users`操作，与其他的GET和POST也不会冲突。
  但这种方式不适合总条数会随时更新的情况，即新的总数不会在换页时立即刷新出来，此时可以选择使用内容与总条数合一的API，也可以定时刷新总数，具体可以根据需求来，没有一劳永逸的解决方案。