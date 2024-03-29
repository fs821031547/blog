---
title: 设计模式 - Observer 观察者模式
date: 2021-08-23 21:21:36
categories: "前端"
---

# Observer（观察者模式）

Observer（观察者模式）属于行为型模式。

**意图：定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并被自动更新。**

拿项目的 npm 依赖举例子：npm 包与项目是一对多的关系（一个 npm 包被多个项目使用），当 npm 包发布新版本时，如果所有依赖于它的项目都能得到通知，并自动更新这个包的版本号，那么就解决了包版本更新的问题，这就是观察者模式要解决的基本问题。

## 举例子

如果看不懂上面的意图介绍，没有关系，设计模式需要在日常工作里用起来，结合例子可以加深你的理解，下面我准备了三个例子，让你体会什么场景下会用到这种设计模式。
<!-- more -->
### 对象与视图双向绑定

在 [精读《设计模式 - Proxy 代理模式》](https://github.com/dt-fe/weekly/blob/v2/178.%E7%B2%BE%E8%AF%BB%E3%80%8A%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%20-%20Proxy%20%E4%BB%A3%E7%90%86%E6%A8%A1%E5%BC%8F%E3%80%8B.md) 中我们也提到了双向绑定概念，只不过代理是实现双向绑定的一个具体方案，而观察者模式才是在描述双向绑定这个概念。

观察者模式在最初提出的时候，就举了数据与 UI 相互绑定的例子。即同一份数据可以同时渲染为表格与柱状图，那么当操作表格更新数据时，如何让柱状图的数据也刷新？从这个场景引出了对观察者模式的定义，即 “数据” 与 “UI” 是一对多的关系，我们需要一种设计模式实现当 “数据” 变化时，所有依赖于它的 “UI” 都得到通知并自动更新。

### 拍卖

拍卖由一个拍卖员与多为拍卖者组成。拍卖时，由 A 同学喊出的竞价（我出 100）就是观察者向目标发出的 `setState` 同时，此时拍卖员喊出（有人出价 100，还有更高的吗？）就是一个 `notify` 通知行为，拍卖员通知了现场竞价全员，刷新了他们对当前最高价的信息。

### 聊天室

聊天室由一个中央服务器与多个客户端组成。客户端发送消息后，就是向中央服务器发送了 `setState` 更新请求，此时中央服务器通知所有处于同一聊天室的客户端，更新他们的信息，从而完成一次消息的发送。

## 意图解释

数据与 UI 的例子已经详细说明了其意图含义，这里就不赘述了。

## 结构图

<img width=600 src="https://img.alicdn.com/imgextra/i4/O1CN011HxE9E24luDnEQiqA_!!6000000007432-2-tps-1774-670.png">

- Subject: 目标，即例子中的 “数据”。
- Observer: 观察者，即例子中的 “表格”、“柱状图”。

还是以数据与 UI 同步为例，当表格发生操作修改数据时，表格这个 TableObserver 会调用 Subject（数据）的 `setState`，此时数据被更新了。然后数据这个 `Subject` 维护了所有监听（包括表格 `TableObserver` 与柱状图 `ColumnChartObserver`），此时 `setState` 内会调用 `notify` 遍历所有监听，并依次调用 `Update` 方法，每个监听的 `Update` 方法都会调用 `getState` 获取最新数据，从而实现表格更新后 -> 更新数据 -> 表格、柱状图同时刷新。

为了更好的理解，以这张协作图为例：

<img width=600 src="https://img.alicdn.com/imgextra/i1/O1CN01QuF29i1RpKAEcCPrX_!!6000000002160-2-tps-1578-728.png">

- `aConcreteSubject`: 对应例子中的数据。
- `aConcreteObserver`: 对应例子中的表格。
- `anotherConcreteObserver`: 对应例子中的柱状图。

## 代码例子

下面例子使用 typescript 编写。

> PS: 为了简化处理，就不定义 Subject 接口与 ConcreteSubject 了，而是直接用 Subject 类代替。Observer 也同理。

```typescript
// 目标，管理所有观察者
class Subject {
  // 观察者数组
  private observers: Observer[] = []
  // 状态
  private state: State

  // 通知所有观察者
  private notify() {
    this.observers.forEach(eachObserver => {
      eachObserver.update()
    })
  }

  // 新增观察者
  public addObserver(observer: Observer) {
    this.observers.push(observer)
  }

  // 更新状态
  public setState(state: State) {
    this.state = state
    this.notify()
  }

  // 读取状态
  public getState() {
    return this.state
  }
}

// 观察者
class Observer {
  // 维护目标
  private subject: Subject

  constructor(subject: Subject) {
    this.subject = subject
    this.subject.addObserver(this)
  }

  // 更新
  public update() {
    // 比如渲染表格 or 渲染柱状图
    console.log(this.subject.getState())
  }
}

// 客户端调用
const subject = new Subject()
// 创建观察者
const observer1 = new Observer(subject)
const observer2 = new Observer(subject)
// 更新状态
subject.setState(10)
```

## 弊端

不要拘泥于实现形式，比如上面代码中的例子，`subject` 与 `observer1`、`observer2` 是一对多的关系，但不一定非要用这种代码组织形式来实现观察者效果。我们也可以利用 Proxy 很轻松的实现：

```typescript
const obj = new Proxy(obj, {
  get(target,key) {}
  set(target,key,value) {}
})

renderTable(obj)
renderChart(obj)
```

我们可以在 `obj` 被任意一个组件访问时触发 `get`，进而对 UI 与视图进行绑定；被任意一个组件更新时触发 `set`，进而对所有使用到的视图进行刷新。使用设计模式切记不要死板，理解原理就行了，在不同平台有不同的更加优雅的实现方式。

## 总结

观察者模式是非常常用的设计模式，它描述了对象一对多依赖关系下，如何通知并更新的机制，这种机制可以用在前端的 UI 与数据映射、后端的请求与控制器映射，平台间的消息通知等大部分场景，无论现实还是程序中，存在依赖且需要通知的场景非常普遍。