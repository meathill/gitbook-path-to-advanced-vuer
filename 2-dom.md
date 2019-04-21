DOM 操作，还需要么？
=================

不用问，答案自然是：**需要**。

MVVM 最重要的特性：数据双向绑定。与 jQuery 时代相比，Vue 无比强调“数据驱动”。哪怕是表示加载的小菊花，也要设置一个变量控制它：

```js
// jQuery
$('button').append('<i class="fas fa-spin fa-spinner"></i>');

// vue
this.isSaving = true;
```

```pug
button
  i.fas.fa-spin.fa-spinner(v-if="isSaving")
  i.fas.fa-check(v-else)
```

不过这并不意味着 Vue 里就不需要操作 DOM 了。通常来说，以下几种情况，需要我们手动更新 DOM：

1. 对象并非 HTML 渲染的来，比如 `<canvas>` 绘制的图形
2. 对象有着特有的渲染机制，比如 CodeMirror 生成的编辑器、图表如 EChart 等

以 CodeMirror 举例，使用 Vue 操作 DOM 要注意两点：

## 1. 选择合适的钩子函数

CodeMirror 需要配合容器的宽高计算每屏的行数。所以它要求容器必须已经渲染，所以我们只能在 `mounted` 钩子里初始化 CodeMirror，否则可能会得到不符合预期的效果。

图表也是如此，有些图表库在获取不到父容器宽高的时候，会默认取一个值，通常也不是我们想要的，所以都要注意 DOM 创建和插入的时机。

## 2. 选择合适的时机重绘

Vue 会缓存数据修改，并且在下一个事件循环“tick”中更新 DOM。换言之，假设这种情况：

```js
this.height = 500;
this.codeMirror.refresh();
```

实际上，因为此时 Vue 只是把 `this.height = 500` 存入重绘队列，真正的重绘还没有发生，容器高度没有变化，所以后面 `codeMirror.refresh()` 也不会生效。

此时我们需要 `$nextTick()`，等到下个事件周期再重绘：

```js
this.height = 500;
await this.$nextTick();
this.codeMirror.refresh();
```

