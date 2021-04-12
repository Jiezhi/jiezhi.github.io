title: GraphQL简介
author: Jiezhi.G
email: Jiezhi.G@gmail.com
premalink: fix_gravatar
thumbnailImage: >-
  http://qn-cdn.nospider.net/2018-06-10-graphql.png
metaAlignment: center
comments: true
date: 2018-06-10 15:02:51
url:
tags:
  - GraphQL
  - Translate
categories:
  - GraphQL
photos:
  - http://qn-cdn.nospider.net/2018-06-10-graphql.png
---
GraphQL会是取代REST API的下一代标准么？

<!--more-->
# 什么是GraphQL
![graphql](http://qn-cdn.nospider.net/2018-06-10-graphql.png)
> GraphQL是比REST更高效、强大和灵活的新一代API标准。Facebook开发了GraphQL并且将其开源，目前其由一大群来自全球各地的公司和个人维护。

注意到GraphQL是API标准，不要看到QL结尾就以为其是一种数据库技术。

## 比REST更灵活的一种选择
REST是目前比较流行的一种暴露服务端数据的常见方式，其简化了客户端尤其是移动端和服务器交互的流程。但是随着业务变得复杂，有些情况变得棘手：

1. 移动端数量的增多，对数据的效率要求变高
移动端和PC端相比，是需要提高对数据获取的效率的，这个效率就是说要减少网络请求、要减少无用数据的传输。

2. 应对复杂的前端框架和平台
现在的情况是仅维护一套API来应对不同框架和平台的请求。PC端一个页面比移动端一个页面展示的内容要多很多，之前后端提供给PC端的API如果直接提供给移动端来使用势必造成资源浪费。所以移动端的人会去找后端的人干一架，结果要么是后端再给移动端单独写一套API，要么就是移动端忍受着API请求返回数据中存在大量冗余的数据。

3. 需要更快速地迭代更新
互联网时代最大的特色除了加班也许就是快了。好多公司在喊着小步快跑、快速试错，毕竟市场不等人。然而REST标准的API似乎很难快速地跟上这快跑的节奏。也许一个API刚出来，产品那边已经改了原型，界面重新设计了。这时候就要麻烦后端同学加个班把接口改一下吧。

## 谁在用GraphQL
一个产品的流行，肯定是解决了目前的某些痛点。虽然GraqhQL目前在国内还不算流行，可是在美利坚已经有不少巨头在使用了：
![](http://qn-cdn.nospider.net/2018-06-10-15286168671382.jpg)
---

# GraphQL vs REST
我们来看一下对于不同API标准下，从服务端获取数据的区别。比如在REST API标准下，有三个接口：

* /users/<id>
该接口返回某用户基本信息

* /users/<id>/posts
该接口返回某用户所有的文章

* /users/<id>/followers
该接口返回某用户所有的关注者

![](http://qn-cdn.nospider.net/2018-06-10-15286174419940.jpg)

*如图所示，要通过三个不同的请求才能获得某用户及其文章和关注者的信息，其中还存在很多不需要的信息。 *

再看一下GraphQL API的实现：
![](http://qn-cdn.nospider.net/2018-06-10-15286175800503.jpg)
*客户端声明自己想要的信息，然后服务端根据请求返回相应的数据*


目前可见的优点：

1. 避免了REST API中常见的信息过多或过少的问题
信息过多是指，接口中总会存在客户端不需要的信息，信息过少是指单条接口无法满足客户端需求，需要请求多个接口才能满足需要
2. 前端可以快速迭代
在REST API中，一般都是后端定义好了API，返回固定的数据格式。当前端业务或需求发生变化时，后端很难跟上变动的节奏。如今，业务变化已经难以避免，所以当前端和后端都要相应地作出改动，这样效率势必降低。就我们公司业务来讲，很多情况下，前端一两天的改动如果再拉上后端，人多肯定要开会再加上沟通成本的问题，这个需求没个一周两周很难搞定。设想一下，如果在GraphQL标准下，除非大的改版，后端基本不用出人力来跟着一起需求评审，前端自己定义查询的内容就搞定了。

3. 更深层次地进行分析
当客户端可以选择自己想请求数据的内容时，这时候就可以分析出哪些信息是用户感兴趣的，也可以更深层次地分析现有数据是如何被应用的。
此外，也可以分析出哪些信息用户不再感兴趣了。

4. Schema & Type系统的优点
GraphQL使用一种强类型系统来定义API，所有的API都通过GraphQL模式定义语言（Schema Definition Language，SDL）来暴露类型数据。而这个模式就是服务端和客户端之间的协议，通过此模式我们可以知道服务端可以提供哪些数据，而客户端又可以获取哪些数据。

一旦模式定义好了，前后端就可以据此独立进行开发了。

# 核心概念
## 模式定义语言（The Schema Definition Language，SDL）
比如这里定义了一个`Person`类型：

```
type Person {
  name: String!
  age: Int!
}
```
Person类型又两个字段，`name`和`age`，分别为`String`和`Int`类型。`!`表示该字段是必须的。


同时，两种类型之间可以有关联，比如`Person`可以和`Post`关联：

```
type Post {
	title: String!
	author: Person!
}
```

相应的，`Person`中也可以关联`Post`类型：

```
type Person {
	name: String!
	age: Int!
	posts: [Post!]!
}
```
其中`Person`和`Post`是一对多的关系

## 通过Queries获取数据
在请求数据之前，你需要GraphQL-IDE来模拟下面的请求，有[Web版](https://api.graph.cool/simple/v1/cjhvs1vtt4ahm012322aexl4n/?query=%7B%0A%20%20allPersons%20%7B%0A%20%20%20%20name%0A%20%20%7D%0A%7D) 也有
[客户端](https://github.com/graphcool/graphql-playground) 

如果你选择了客户端，在启动页面打开这个地址： https://api.graph.cool/simple/v1/cjhvs1vtt4ahm012322aexl4n/

![](http://qn-cdn.nospider.net/2018-06-10-15286229140756.jpg)

让我们来一个最简单的请求：

![](http://qn-cdn.nospider.net/2018-06-10-15286230297031.jpg)

```
{
  allPersons {
    name
  }
}
```
可以看到返回的结果：

```
{
  "data": {
    "allPersons": [
      {
        "name": "Johnny"
      },
      {
        "name": "Sarah"
      },
      {
        "name": "Alice"
      },
      {
        "name": "Bob"
      },
      {
        "name": "Alice"
      }
    ]
  }
}
```

如果我们想在结果里带上age的话：

```
{
  allPersons {
    name
    age
  }
}
```
![](http://qn-cdn.nospider.net/2018-06-10-15286232389082.jpg)

### 带参请求
同样没问题，可以看到只返回了最后两条数据：
![](http://qn-cdn.nospider.net/2018-06-10-15286233483934.jpg)

## 修改数据
这里说的修改包含三种操作：

* 创建数据
* 更新数据
* 删除数据

这里以创建数据为例

```
mutation {
  createPerson(name: "Bob", age: 36) {
    name
    age
  }
}
```
![](http://qn-cdn.nospider.net/2018-06-10-15286234981761.jpg)

## 订阅实时更新
GraphQL提供了`subscriptions`的概念来提供给客户端订阅事件。当客户端订阅某事件后，其将会和服务端保持一个稳定的链接。当特定事件触发时，服务端会推送对应的数据给客户端。

比如我们想订阅新建`Person`类型事件：

```
subscription {
  newPerson {
    name
    age
  }
}
```
当有新的Person类型被创建时，客户端就能接收到更新的数据了。

## 定义模式
模式是GraphQL API中最重要概念之一，其定义了API能提供哪些数据，以及客户端如何获取这些数据。

有几个特殊的类型被称之为`root`类型：

```
type Query { ... }
type Mutation { ... }
type Subscription { ... }
```

本文中用到的完整模式定义如下：

```
type Query {
  allPersons(last: Int): [Person!]!
}

type Mutation {
  createPerson(name: String!, age: Int!): Person!
}

type Subscription {
  newPerson: Person!
}

type Person {
  name: String!
  age: Int!
  posts: [Post!]!
}

type Post {
  title: String!
  author: Person!
}
```

# 结语
至此，大家应该对GraphQL有一个感性的认识了吧。如果不想仅停留在概念层面，你可以到[这里](https://graphql.org/code/gg)找到目前已经实现的框架。（反正我已经用起来了:P）
![](http://qn-cdn.nospider.net/2018-06-10-15286245421358.jpg)

更多内容可访问以下网站：

https://www.graphql.org/
https://www.howtographql.com/
