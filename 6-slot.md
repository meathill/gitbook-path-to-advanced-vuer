组件插槽的使用
============

组件插槽是个非常好的特性，它可以大大提升组件的复用可能。插槽使用其实很简单，看官方文档：[插槽](https://cn.vuejs.org/v2/guide/components-slots.html) 就能了解大概。这里我只介绍作用域插槽，使用它可以方便我们创建更有用的组件。

插槽的作用，是把父组件里渲染好的内容插到子组件的特定位置，这样，子组件的适用范围就广阔很多，比如 Modal 弹窗组件，写组件的时候完全无法估计窗体里要放什么内容，几乎 100% 要交给父容器决定。

但是接下来又有个问题：有时候我们需要从子组件里获取处理过的数据，或者让子组件决定应该显示哪些数据。这个时候，简单的 `slot` 就不够用了，需要用到“作用域插槽”。

比如表格组件。一般来说，我们习惯按列构造表格，因为通常来说，每一列的内容是高度一致的，比如都是 ID、都是名称（字符串）、都是某个统计结果（数值）。

一个简单的表格组件模板如下所示：

```pug
table
  thead
    tr
      th(v-for="col in cols") {{col.label}}
  tbody
    tr(v-for="item in list")
      td(v-for="col in cols") {{item[col.field]}}
```

这样能满足简单展示的需求，不过对于“删除行”、“临时禁用”行之类的需求，就不够了。此时我们可以使用“作用域插槽”，引入“动作单元格”。创造组件的时候，我们无法预期最终组件里会放哪些动作，所以留一个空插槽对外即可。

```pug
    tr(v-for="item in list")
      td(v-for="col in cols")
        slot(v-if="col.isAction", name="action-cell", :row="item")
          // this is action cell

        template(v-else) {{item[col.field]}}

```

这样，如果我们要增加一个“删除”按钮和一个“禁用” toggle，就可以这样：

```pug
meathill-table
  template(#action-cell="{row}")
    .btn-group-sm
      button.btn.btn-danger(
        type="button",
        @click="doDelete(row)",
      )
        i.fas.fa-trash

      toggle(
        :value="row.enabled",
        @change="doToggle(row)",
      )
```

这里的 `{row}` 表示我们要对子元素传出来的值进行结构，找出其中定义为 `row` 的值，进行处理。

总结
--------

作用域插槽是组件中非常重要的概念，使用范围非常广，大家一定要好好掌握它。

--------

> 注：Vue 2.6 之后，使用新的 `v-slot` 取代之前的 `slot`，旧的插槽机制仍然有效，但是会在 Vue 3 里被彻底废弃。建议大家都好好把上面提到的官方文档读几遍，然后逐步重构到新的插槽机制。
