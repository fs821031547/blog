---
title: Web前端开发规范.md
date: 2020-02-23 19:24:20
categories: "前端"
---

# Web前端开发规范

## 开发规范

### 环境配置

- `node` 版本：采用`v14.x.x`版本或最新稳定版本
- `npm` 版本：采用最新版本
- `vue-cli` 版本：采用最新稳定版本
- `vue` 版本： 采用`3.x.x`版本

### 项目配置

#### editorconfig
<!-- more -->
`.editorconfig` 文件有助于为不同 IDE 编辑器上处理同一项目的多个开发人员维护一致的编码风格。

```
# http://editorconfig.org
# 告诉EditorConfig插件，这是根文件，不用继续往上查找
root = true

# 匹配全部文件
[*]
# 设置字符集
charset = utf-8
# 缩进风格，可选space、tab
indent_style = tab
# 缩进的空格数
indent_size = 2
# 结尾换行符，可选lf、cr、crlf
end_of_line = lf
# 在文件结尾插入新行
insert_final_newline = true
# 删除一行中的前后空格
trim_trailing_whitespace = true

# 匹配md结尾的文件
[*.md]
insert_final_newline = false
trim_trailing_whitespace = false
```

#### UI框架选择

- PC端Vue项目UI框架优先选择：`Element UI`
- 移动端Vue项目UI框架：`Vant-ui`

### 编程规范

#### 变量方法及文件名定义

- 定义变量时，常量使用`const`定义，全大写下划线格式命名。

- 变量定义使用小驼峰命名方式根据变量用途语义化命名。

- 方法命名同变量命名方式采用小驼峰根据用途命名。

- 文件命名同上小驼峰命名。
  - 页面`xx.vue`文件根据页面用途命名，文件放置该页面对应模块文件夹下。
  - 页面`xx.scss`文件同对应页面名，放于该页面对应模块样式文件夹下。
  - `xx.js`文件，同上根据用途命名，

```
# 文件命名示例
|— userCenter
	|— userCenter.vue
	|— userCenter.js
	|— userCenter.scss
```

```javascript
// 常量
const USER_NAME = 'XiaoChen';
// 变量
let userName = 'XiaoChen';
// 方法
let changeUserName = (userName) => {};
```

#### 页面排版规范

- 文本输入框需设置默认提示语，文本框长度若无`UI`情况下限制不超过`300px`宽度。

- 下拉选择框（包含下拉选框、联级选框）需设置默认提示语，长度若无`UI`情况下限制不超过`300px`宽度。

- 搜索表单：置于页面内容部分上方，采用`inline`模式`el-form`排版，此时输入框、下拉框长度无需设置。

- 新增/编辑表单：根据复杂程度及对应需求选择使用弹框形式或新页面形式展示，采用竖排样式排版，需设置文本框及下拉框长度，并统一设置`label`长度，对于表单必填项需进行参数校验。

  > 参数校验时如使用正则校验参数，如手机号，需考虑全号段情况，也可只校验长度。

- 列表详情页面：视复杂程度及对应需求要求选择使用弹框形式或新页面形式展示，如无特殊情况可与新增/编辑页面重用。

- 在排版页面时还需考虑数据空态页面展示。

#### 样式规范

- 选用 `sass` 或 `less`。
- 公共样式文件内除特殊情况仅可进行新增操作，不可对原样式值进行修改。
- 单个组件样式可直接写到组件下 `style` 标签下，为了防止样式污染，需添加 `scoped  ` 属性，也可以通过设置作用域来防止样式污染，写样式时需使用用类选择器，尽量避免使用元素选择器。

#### 页面编码规范

- 组件名称必须以大驼峰法命名
- 变量及方法命名需符合规范及语义化定义
- 对于页面初始进入需请求接口等操作是应放置于生命周期 `mounted` 函数内进行
- 代码中不可出现默认具体值出现，默认值一律为空字符串、空对象、空数组等，需要设置默认值时可另开方法操作，提测时要关闭该方法调用；本地存储相关默认值在 `Chrome` 的开发工具中 `Application` 中对应存储类型内配置默认值
- 计算函数及侦听器需注意使用场景，`computed` 计算属性仅可用于简单运算，涉及大量运算或存在异步操作时需改用 `watch`
- 开发期所有 `console` 应添加标识位，打包上线时要关闭所有 `console` 
- 对于 `H5` 页面即需要在移动端展示的页面项目需添加 `vconsole` 依赖，便于调试，开发期可开启调试展示，打包上线时要关闭展示
- 提交类型的按钮需要设置 `loading` 展示
- 对于短时间多次触发接口调用的方法需要添加事件防抖机制

#### 接口调用规范

- 接口地址需统一于`/src/api/requestUrl.js` 文件中依据模块进行分组定义

  ```javascript
  // 命名格式： 接口名+请求方式——小驼峰 (restful接口时仅接口名定义即可)
  // ****** 授权模块 ****** //
  const AUTH_API = {
    loginPost: '/auth/login',
    registerPost: '/auth/register',
    codeGet: '/auth/code',
  }
  // ****** 授权模块 ****** //
  // ****** 用户模块 ****** //
  const USER_API = {
    listGet: '/user/list',
    userUpdatePut: '/user/userUpdate',
    user: '/user', // restful 风格，合并 userGet/userPost/userPut/userDelete
  }
  // ****** 用户模块 ****** //
  export {
  	AUTH_API,
    USER_API,
  }
  ```

- 接口请求方法需于 `/src/api` 目录下依据模块新建对应文件内进行定义

  ```
  |- src
  	|- api
  		|- requestUrl.js
  		|- auth.js
  		|- user.js
  ```

  ```javascript
  // auth.js
  import request from '@/utils/request';
  import {AUTH_API} from '@/api/requestUrl';
  /**
   * 登录
   * @param username {String}
   * @param password {String}
   * @param code {String}
   * @param uuid {String}
   * @returns {Promise<any>}
   */
  export function login(username, password, code, uuid) {
    return request({
      url: AUTH_API.loginPost,
      headers: {
        isToken: false
      },
      method: 'post',
      data: { username, password, code, uuid }
    })
  }
  ```

- 接口调用时需添加用户加载提示，适当添加`loading`展示，分页加载时：

  - 滑动加载，加载提示可展示在最后一项后添加。
  - 分页器分页加载时，加载提示可采用全屏`loading`形式展示。

  操作类接口调用时，在对应操作按钮上添加`loading`展示

  > 如对应操作按钮在列表中则采用全局`loading`形式展示

- 接口调用结果视情况提示用户：

  - 加载成功：
    - 加载数据时可直接展示数据，无需提示
    - 操作类接口调用成功时，添加`操作成功`提示弹框，可描述为对应操作，如`下架成功`。
  - 加载失败：若接口有返回错误信息时，将后台返回结果提示给用户；若无返回错误信息时提示用户`服务异常`.

- 接口文件定义：

  - 接口地址统一定义在接口配置文件中，使用常量命名定义；

  - 接口调用统一封装至对应模块`xx.js`文件中，如`user`模块独立定义为`user.js`文件，其内包含增删改查等对应操作的接口调用封装方法，

    > 该方法应返回`Promise`对象，接口请求操作在该方法内部进行。
  
- 接口提交数据时，对文本框输入信息进行两端去空处理。

## 发版规范

### 仓库分支管理

[Git 分支管理规范](./git分支管理规范.md)

### 版本发布

- 当期需求发布时需列出当期需求所有功能点即 `版本CheckList` 并与对应产品经理一一确认后才可进行发版操作。

- 发版完成后需再次与对应产品经理进行功能点确认工作，并及时联系 `版本负责人/项目负责人` 进行代码合并等操作。

### 版本回滚方案
若当前发布版本产生重大问题或其他原因导致需要进行生产环境代码回滚时，需由 `版本负责人/项目负责人` 从 发版 `前一次tag` 派生新 `hotfix` 分支进行发布部署生产环境操作，发布完成后合并至 `master` 分支并生成 `新tag`。
