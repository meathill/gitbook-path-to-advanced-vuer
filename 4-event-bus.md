全局事件总线
==========

面向对象建议我们高内聚、低耦合，每个组件（类）只负责有限的、特定的工作，复杂的工作得通过组合不同组件完成。

但是类的粒度从来都很难抉择。很多时候，粒度大了，接口参数特别多；粒度小了，多个类之间调度又成问题。面对后者，全局事件总线能解决一些问题。

全局事件总线，顾名思义，首先，它是全局的；其次，它使用事件传递信息。它的功能主要用来在彼此隔离的组件之间，快捷方便的传递数据。

用法
--------

虽然 Vue 提供了 `$emit`，`$on`，`$once` 方法，但是我倾向于使用独立的 [EventEmitter3](https://www.npmjs.com/package/eventemitter3) 来创建事件总线。我认为这样能够避免产生歧义，而且 EventEmitter3 使用范围很广，多半已经作为某个类库的依赖安装过了。

实现很简单，我们只需要构建一个 EventEmitter3 实例，然后暴露出去。模块系统会负责维护它。最终只要一个 JS 文件即可：

```js
import EventEmitter3 from 'eventemitter3';

const emitter = new EventEmitter3();

export default emitter;
```

使用的时候，使用模块导入即可：

```js
import bus from './event/bus';

// A 组件，响应事件
export const A = {
  methods: {
    onSomeEvent() {

    },
  },
  beforeMount() {
    bus.on('some-event', () => {
      this.onSomeEvent();
    });
  },
}

// B 组件，广播事件
export const B = {
  beforeMount() {
    bus.emit('some-event', 'hello world');
  },
};
```

例子中的 A、B 两个组件，可以位于任何位置，可以有关系也可以没关系。这就是全局事件的灵活之处。

注意
--------

有句老话说得好：全局一时爽，XX火葬场。事件机制将影响范围控制在最小，但也仍然有影响，所以使用的时候一定要谨慎，切勿滥用。
