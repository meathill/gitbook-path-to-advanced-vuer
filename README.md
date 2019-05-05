Vue 进阶必经之路
==============

前言
========

在众多 MVVM 框架之中，Vue 是后起之秀。但凭借出色的设计、优秀的 API，Vue 后发先至，如果不是 996.ICU 出来搅局，它已经稳坐 GitHub Star 第二把交椅。

大家喜欢 Vue 的原因很多，最重要的一点是：它的学习曲线很平缓。作者开创性地用 `getter/setter` 替换传统的赋值行为，这样一来，虽然写起来还是 `this.a = b;`，但实际上这里的值会立刻被渲染到视图当中，非常神奇，尤如魔法。

然而这样的便利必然有其代价：一旦出现问题，难以解决。就像开车，知道油门，摸一摸方向盘，想把车开走并不难；但是如果不在驾校好好学一学，遇到特殊情况，比如冷天要开空调消前挡风玻璃的雾气，就寸步难行。我当初也是，正开开心心写着业务逻辑，突然一块页面就不更新了，怎么改都不行，足足卡了我一天。

从那以后，我就开始主动学习吸收 Vue 相关的知识，结合开发时遇到的问题，慢慢的，基本上不会再有哪个问题会卡住我很久了。继而，我也开始学习和总结 Vue 常见功能实现范式，摸索一些最佳实践什么的。现在，我想把这个过程当中积累下来的知识跟大家分享一下，其中包含：

1. Vue 的双向绑定机制
2. Vue 的生命周期
3. 原生 JS
4. Vue 全家桶
5. 组件开发
6. Webpack 相关配置

目标读者
--------

1. 前端水平：初级、中级
2. 有 Vue 开发经验
3. 平时业务代码写的比较多，想学习技术细节的同学

名词及约定
--------

Vue 里没有明确提出类的概念。它的做法比较像工厂模式，`Vue` 是一个工厂，我们写配置 `config`，然后通过 `new Vue(config)` 的方式实例化。不过我仍然习惯将每个配置称为一个“Vue类”，很多时候，一个 Vue 组件，无论用法还是功能，也都相当于面向对象概念中的一个类。所以文中会混用“类”和“组件”，只在某些特定时刻强调它们的不同。

本文写作时，Vue 3 仍在开发当中，关于它的消息已经透露不少。我的行文仍然会以 v2.6 为基础，如果 v3 会出现变化，我尽量备注出来。关于 v3 的一些介绍，我放在附录里面。

为节省时间，范例代码中的 HTML 会以 pug 书写，这种语言很容易读，文中也用不到高级语法，应该问题不大。另外，如果你还在写原生 HTML 或 CSS，我建议你尽快切换到新语言。

其它约定：

1. 文中会混用 `getter` 与 `get`，`setter` 与 `set`，我相信结合上下文你们应该看得懂它的意思
2. 范例代码以 ES6 为基础，也会使用 ES2017+ 新增语法，如果你对这些“新”语法不熟悉，附录里有一些资源方便你学习。

文中代码的目标环境：

1. Vue >= 2.6
2. Vuex >= 3.1
3. Vue-Router >= 3.0
4. Webpack >= 4.29.6
5. Node.js >= 10.15

作者介绍
--------

大家好，我叫翟路佳，花名“肉山”，这个名字跟 Dota 没关系，从高中起伴随我到现在。

我热爱编程，喜欢学习，喜欢分享，从业十余年，投入的比较多，学习积累到的也比较多，对前端方方面面都有所了解，希望能与大家分享。

我兴趣爱好比较广泛，尤其喜欢旅游，欢迎大家相互交流。

我目前就职于 OpenResty Inc.，定居广州。

你可以在这里找到我：

* [博客](https://blog.meathill.com)
* [微博](https://weibo.com/meathill)
* [GitHub](https://github.com/meathill)

或者通过 [邮件](mailto:meathill@gmail.com) 联系我。

--------

限于个人能力、知识视野、文字技术不足，文中难免有疏漏差错，如果你有任何疑问，欢迎在任何时间通过任何形式向我提出，我一定尽快答复修改。
