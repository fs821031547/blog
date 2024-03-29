---
title: 精读《Nuxtjs》.md
date: 2021-04-16 19:13:17
categories: "前端"
---
## 1 引言

[Nuxt](https://github.com/nuxt/nuxt.js) 是基于 Vue 的前端开发框架，这次我们通过 [Introduction toNuxtJS](https://www.youtube.com/watch?v=NS0io3Z75GI) 视频了解框架特色以及前端开发框架的基本要素。

> nuxt 与 [next](https://github.com/zeit/next.js) 结构很像，可以结合在一起看

视频介绍了 NuxtJs 的安装、目录结构、页面路由、导航模版、asyncData、meta、vueX。

这是一个入门级视频，所以上面所列举的特征都是一个前端开发框架的最核心的基本要素。一个前端开发框架，安装、目录结构、页面路由、导航模版一定是最要下功夫认真设计的。

asyncData 和 Vuex 都在解决数据问题，meta 则是通过约定语法控制网页 meta 属性，这部分值得与 React 体系做对比，在精读部分再展开。

Nuxtjs 前端开发框架不仅提供了脚手架的基本功能，还对项目结构、代码做了约定，以减少代码量。从这点可以看出，脚手架永远围绕两个核心目标：**让每一行源码都在描述业务逻辑；让每个项目结构都相同且易读**。
<!-- more -->
20 年前，几百行 HTML、Css、Js 代码就能完成一个完整的项目，只需要遵守 W3C 的基本规范就足够了，每一个项目代码都简单清晰，而且由于没有复杂的业务逻辑，导致代码结构也非常简单。但现在前端项目复杂度逐渐升高，一个大型项目源码数量可能达到几十万行、几百万行，这是 W3C 规范没有设想到的，因此出现了各种工程化与模块化方案解决这个复杂度问题，也引发了各个框架间约定的割裂，且设计合理程度各不相同。

Nuxtjs 等框架要做的就是定义支持现代大型项目的前端研发标准，这个规范具有网络效应，即用的人越多，价值越大。

接下来我们进入正题，看看 Nuxt 脚手架定义了怎样的开发规范。

## 2 概述

### 安装

使用 `npx create-nuxt-app app-name` 创建新项目。这个命令与 `create-react-app` 一样，区别主要是模版以及配置不同。

这个命令本质上是拉取一个模版到本地，并安装 `nuxt` 系列脚本作为项目依赖，并自动生成一系列 npmScripts：

```json
{
  "scripts": {
    "dev": "nuxt",
    "build": "nuxt build",
    "start": "nuxt start",
    "generate": "nuxt generate",
    "lint": "eslint --ext .js,.vue --ignore-path .gitignore .",
    "test": "jest"
  },
  "dependencies": {
    "nuxt": "^2.0.0"
  }
}
```

之后即可通过 `npm start` 等命令开发项目，对大部分项目来说，npmScripts 启动是最能达成共识的。

这种安装方式另一个好处是，依赖都被安装在了本地，即开发环境 100% 内置在项目中。Nuxt 没有采用全局 cli 命令方式执行，第一是 npmScripts 更符合大家通用习惯，不需要记住不同脚手架繁琐的名称与不同约定的启动命令，第二是全局脚手架一旦进行不兼容升级，老项目就面临维护难题。

### 目录结构

```text
├── .nuxt
├── layouts
├── pages
├── store
├── assets
├── static
├── middleware
├── plugins
├── nuxt.config.js
```

**pages**

页面文件存放的目录，路径 + 文件名即路由名，关于更多约定路由的信息，在下一节页面路由详细说明。

**layouts**

模版文件存放的目录，文件名即模版名，页面可以通过定义模版在选择使用的模版。

**store**

全局数据流目录，在 vueX 章节介绍。

**assets**、**static**

分别存放不需被编译的资源文件与非 `.vue` 的静态文件，比如 scss 文件。

由于 `.vue` 文件集成了 html、js、css，因此一般不会再额外定义样式文件在 static 文件夹中。

当然，这是 Vue 生态的特别之处，在 React 生态中会存在大量 `.scss` 文件混杂在各个目录中，比较影响阅读。

**middleware**、**plugins**

中间件与插件，这两个目录是可选的，作为一种定制化拓展能力。

**.nuxt**

为实现约定路由等便捷功能，启动项目时需要自动生成一些文件作为真正项目入口，这些文件就存储在 `.nuxt` 目录下，gitingore 且无需手动修改。

**nuxt.config.js**

nuxt 使用 js 文件作为配置文件，比 json 配置文件拓展性更好一些，这个文件也是整个项目唯一的配置文件。

基本上 **pages**、**layouts**、**store**、**assets**、以及唯一的配置文件基本成为现代前端开发框架的标配。

### 页面路由

nuxt 支持约定路由：

```text
├── pages
│   ├── home.vue
│   └── index.vue
```

上述目录结构描述了两个路由：`/` 与 `/home`。

也支持参数路由，只要以下划线作为前缀命名文件，就定义了一个动态参数路由：

```text
├── pages
│   ├── videos
│   │   └── _id.vue
```

`/videos/*` 都会指向这个文件，且可以通过 `$route.params.id` 拿到这个 url 参数。

另一个特性是嵌套路由：

```text
├── pages
│   ├── videos
│   │   └── index.vue
│   └── videos.vue
```

`videos.vue` 与 `videos/index.vue` 都指向 `/videos` 这个路由，如果这两个文件同时存在，那么外层的 videos 就会作为外层拦截所有 `/videos` 文件夹下的路由，可以通过 `nuxt-child` 透出子元素：

```html
# pages/videos.vue
<template>
  <div>
    videos
    <nuxt-child />
  </div>
</template>
```

### 导航模版

页面公共逻辑，比如导航条可以放在模版里，模版的目录在 `layouts` 文件夹下。

默认 `layouts/default.vue` 对所有页面生效，但也可以创建例如 `layouts/videos.vue` 特殊导航文件，在 `pages/` 页面文件通过如下申明指定使用这个模版：

```html
<script>
  export default {
    layout: "videos"
  };
</script>
```

### asyncData

`asyncData` 是 nuxt 支持的异步取数函数，可以替代 `data`。

`data` 函数：

```html
<script>
  export default {
    data() {
      return {};
    }
  };
</script>
```

对于异步场景，可以用 `asyncData` 替代：

```html
<script>
  export default {
    async asyncData() {
      return await fetch("/");
    }
  };
</script>
```

### meta

nuxt 允许在 `.vue` 页面文件自定义 head 标签信息：

```html
<script>
  export default {
    headr() {
      return {
        title: "",
        meta: {
          charset: "utf-8"
        }
      };
    }
  };
</script>
```

这是开发框架提供的特性，不过在 React 体系下可以通过 `useTitle` 等自定义 Hooks 解决此问题，将框架功能降维到代码功能，会更容易理解些。

### vueX

nuxt 集成了 [vuex](https://github.com/vuejs/vuex)，在 `store/` 文件夹下创建数据模型：

```js
export const state = () => ({
  videos: [],
  currentVideo: {}
})

export const mutations = {
  SET_VIDEOS (state, videos) {
    state.videos = videos
  }
  SET_CURRENT_VIDEO (state, video) {
    state.currentVideo = video
  }
}
```

接下来就能在 `pages` 文件夹下的页面组件使用了：

```html
<script>
  import { mapState } from "vuex";

  export default {
    async fetch({ $axios, params, store }) {
      const reponse = await $axios.get(`/videos/${params.id}`);
      const video = response.data.data.arrtibutes;
      store.commit("SET_CURRENT_VIDEO", video);
    }
  };
</script>
```

将 `return` 替换为 `store.commit` 即可，更多语法可以参考 [vuex 文档](https://github.com/vuejs/vuex)。

## 3 精读

Nuxtjs 框架做了几件事情：

1. 统一执行命令。
2. 统一开发框架。
3. 统一目录与代码规范。
4. 内置公共 utils 函数。

### 统一执行命令

命令行是所有开发者每天都要用上十几次甚至几十次的场景，试想一下团队中项目分别有如下这么多不同的启动命令会怎么样？

1. npm start.
2. monkey dev.
3. npm run ng.
4. npm run bootstrap & banana start.
5. ...

我永远不知道下一个项目该如何启动，这大大降低了开发效率。更严重的是，有的项目可以通过 `npm run docs` 查看文档，有的项目不能；有的项目 `npm run build` 可以触发编译，有的项目却无需编译，等等，所谓的环境不一致或者说迁移成本，学习成本，都是由最开始负责搭建项目脚手架的同学对架构设计不一致导致的，**然而没有必须用 `monkey dev` 才能运行起来的项目，但项目却可能因为被设计为 `monkey dev` 启动而显得与其他项目格格不入，甚至难以统一维护。**

Nuxtjs 等前端开发框架统一执行命令就是为了解决这个问题，统一开发者习惯需要很长的时间周期，但这个趋势不可挡。

### 统一开发框架

**虽然现在 React、Vue、Angular 框架各有利弊，但如果一个团队的项目同时使用了两个以上的框架，没有人会觉得这是一件好事。**

诚然每个框架都有自己的特点，在不同维度都一些优势，但三大框架能并存，说明各自都没有绝对的杀手锏来消灭对方。

对开源来说，多元化是活力的源动力，但对一家公司来说，多元化就是一场灾难，至今没有一个框架敢说自己的优势是 “与其他框架混合使用可以提升整体开发效率”。

前端开发框架要解决的最重要问题也是这一点，无论如何只能选择一种开发框架，Nuxtjs 选择了 Vue，Nextjs 选择了 React。

### 统一目录与代码规范

目录和代码规范不会从根本上影响项目的通用性，因为不同的目录结构可以通过映射来兼容，不同的代码规范不会影响代码执行。所以目录与代码规范真正影响的是一个程序员对项目的 “解码成本”。

所谓解码成本，就是程序员理解项目逻辑所需要的成本。如果你是一个销售主管，让团队周报统一用一种格式汇总绝对比 “用自己喜欢的方式汇总” 效率高，而对编程也一样，一个完全不同的目录结构和代码规范对程序员来说是巨大的阅读阻碍，甚至可能引发恶心反应。

所以不同的目录结构和代码规范是没有必要的壁垒，除非你的团队已经对某种规范产生达成了牢固的共识，否则最好和其他团队共享相同的目录结构与代码规范。改变代码规范是一件很难得事情，但只要不同规范的团队间产生了长期合作关系，规范统一就势必会被提上议程，那么为何不能在公司层面早一点达成共识，提前消除这种痛苦呢？

所以统一目录与代码规范是前端开发框架需要优先确定的，很多时候不要去质疑为什么目录叫 `layouts` 而不叫 `layout`，因为这个规范背后形成的协同网络规模越大，叫什么名字就越不重要。

### 内置公共 utils 函数

让业务开发更聚焦，还可以通过抽取通用的逻辑的方式解决，但需要解决两个问题：

1. 虽然将公共函数抽成 npm 包可以解决代码复用问题，但关键是怎么保证你的代码能被别人复用？
2. 如何让业务通用的 utils 代码有效沉淀并从项目中移除？

脚手架内置公共 utils 函数就为了解决这个问题。上面几个小节解决了通用命令、框架、规范，但实际代码中，`router` `history` `fetch` `store` 等等概念也都是可以统一的，**没有一个项目必须用定制的 `fetch` 函数才能取数，但一开始就定制了 `fetch` 会导致耦合了不可预期的、没有必要的业务逻辑，成为理解与提效的阻碍。**

所以统一这些能统一的包，是进一步提效的关键。也许有人会觉得断了自己造轮子的路，但就像我们如今都不会重写浏览器内核逻辑一样，稳定的逻辑不仅带来了全行业的提效，还催生了前端岗位带来大量的就业，同样的，统一底层通用函数，其实是断了无意义产出这条路，每个人都有追求更高价值事情的权利，不要把自己困在反复造 `fetch` 函数这个低水平的活里。

## 4 总结

如果一个项目没有使用类似 Nuxtjs 开发框架，它面临的不仅仅是技术选型不统一的问题，久而久之这种项目势必成为 **代码孤岛**，当尘封在代码仓库几年后，一系列文档工具链接都失效后，就成为谁也不想碰，不敢碰的高危代码。

所以我们今天不仅要看到 Nuxtjs 提供的能力对项目开发有多么便捷，更要看到这类框架带来的协同效应有多么巨大，如果它不能成为整个前端的标准，至少要成为你们公司，或者你们团队的标准。
