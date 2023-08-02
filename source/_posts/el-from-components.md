---
date: 2020-10-15
title: el-from 表单组件封装
categories: [UI]
tags: [element-ui, 组件, 表单, 封装]
cover: https://cdn.xiangshu233.cn/post/banner/wallhaven-wygyrp.jpg
cover_info:
  title: el-from 表单组件封装
banner: https://cdn.xiangshu233.cn/post/banner/wallhaven-wygyrp.jpg
---

## 缘由
公司后台管理表单页过多，且大部分都是普通 `input select` 框，重复度过高，为了避免无用劳动则对 `from` 进行封装

## BasicForm组件

```js
<template>
  <el-form
    ref="BasicForm"
    class="page-form"
    :model="data"
    :rules="rules"
    :label-position="labelPosition"
    :label-width="labelWidth"
  >
    <template v-for="(item, index) in getFieldList()">
      <el-form-item
        v-if="!item.hide"
        :key="index"
        :prop="item.value"
        :label="item.label"
        :class="item.className"
        :style="{ width: `calc(100%/${count} - 50px)` }"
      >
        <!-- solt -->
        <template v-if="item.type === 'slot'">
          <slot :name="'form-' + item.value" />
        </template>
        <!-- 普通输入框 -->
        <el-input
          v-if="item.type === 'input' || item.type === 'password'"
          v-model="data[item.value]"
          clearable
          autocomplete="new-password"
          :size="formSize"
          :maxlength="item.maxlength || 50"
          :show-password="item.type === 'password'"
          :type="item.type"
          :disabled="item.disabled"
          :placeholder="getPlaceholder(item)"
          @blur="handleEvent($event, item.value)"
        />
        <!-- 文本输入框 -->
        <el-input
          v-if="item.type === 'textarea'"
          v-model="data[item.value]"
          :size="formSize"
          :maxlength="item.maxlength || 50"
          :type="item.type"
          :disabled="item.disabled"
          :placeholder="getPlaceholder(item)"
          :autosize="item.autosize || { minRows: 2, maxRows: 10 }"
          @blur="handleEvent($event, item.value)"
        />
        <!-- 计数器 -->
        <el-input-number
          v-if="item.type === 'inputNumber'"
          v-model="data[item.value]"
          :size="formSize"
          v-bind="item"
          @change="handleEvent($event, item.value, 'change')"
        />
        <!-- 选择框 -->
        <el-select
          v-if="item.type === 'select'"
          v-model="data[item.value]"
          :size="formSize"
          v-bind="item"
          :placeholder="getPlaceholder(item)"
          @change="handleEvent($event, item.value, 'change')"
        >
          <el-option
            v-for="childItem in selectOption[item.list]"
            :key="childItem.id"
            :label="childItem.name"
            :value="childItem.id"
          />
        </el-select>
        <!-- 日期选择框 -->
        <el-date-picker
          v-if="item.type === 'date'"
          v-model="data[item.value]"
          v-bind="item"
          :type="item.dateType"
          size="mini"
          style="width: 100%;"
          start-placeholder="开始"
          end-placeholder="结束"
          :placeholder="getPlaceholder(item)"
          @change="handleEvent($event, item.value, 'change')"
        />
      </el-form-item>
    </template>
  </el-form>
</template>
<script>

export default {
  name: 'BasicForm',

  data() {
    return {

    }
  },

  props: {
    /** 实例 */
    refObj: {
      type: Object,
      default: () => ({}),
    },
    /** 表单数据 */
    data: {
      type: Object,
      default: () => ({}),
    },
    /** 验证规则 */
    rules: {
      type: Object,
      default: () => ({}),
    },
    /** 表单相关字段配置 */
    fieldList: {
      type: Array,
      default: () => [],
    },
    /** 下拉列表配置 */
    selectOption: {
      type: Object,
      default: () => ({}),
    },
    /** label 宽度 */
    labelWidth: {
      type: String,
      default: '120px',
    },
    /** label 位置 */
    labelPosition: {
      type: String,
      default: 'right',
    },
    /** 全部禁用 */
    allDisabled: {
      type: String,
      default: null,
    },
    /** 列数量 */
    count: {
      type: [Number, String],
      default: 1,
    },
    /** 大小 */
    formSize: {
      type: String,
      default: 'mini',
    },
  },

  watch: {
    data: {
      handler() {
        this.$emit('update:refObj', this.$refs.BasicForm);
      },
      deep: true,
    },
  },

  mounted() {
    this.$emit('update:refObj', this.$refs.BasicForm);
  },

  methods: {
    getFieldList() {
      let resolveList = JSON.parse(JSON.stringify(this.fieldList))
      // console.log('resolveList',resolveList);
      return resolveList
    },
    /**
     * @method 判断placeholder显示内容
     * @param {Object} row
     * @returns {String} placeholder
     * @desc 📝默认显示内容提示
     */
    getPlaceholder(row) {
      let placeholder;
      if (
        row.type === 'input' ||
        row.type === 'textarea' ||
        row.type === 'password'
      ) {
        placeholder = '请输入' + row.label;
      } else if (
        row.type === 'select' ||
        row.type === 'time' ||
        row.type === 'date'
      ) {
        placeholder = '请选择' + row.label;
      } else {
        placeholder = row.label;
      }
      return placeholder;
    },
    /**
     * @method 绑定相关事件
     * @param {Event} event
     * @param {String | Number} val
     * @param {String} change
     * @desc 📝事件处理, 当前form项失去焦点时, 获取 value 值和 字段名称
     * @desc 📝change 事件特殊处理: change 只能拿到选中值. 此时的 event 为选中值的 value
     */
    handleEvent(event, val, change) {
      let obj = {
        value: change === 'change' ? event : event.target.value,
        label: val,
      };
      this.$emit('handleEvent', obj);
    },
  },
};
</script>
<style lang="scss" scoped>
.el-form-item {
  margin: 0 10px 20px 10px;
  display: inline-block;
  .el-form-item__content {
    .el-input,
    .el-select,
    .el-textarea {
      width: 100%;
    }
    .el-input-number {
      .el-input {
        width: inherit;
      }
    }
  }
  .el-form-block {
    display: block;
    width: 100%;
    .el-form-item__content {
      .el-input,
      .el-select,
      .el-textarea {
        width: 100%;
      }
    }
  }
}
</style>
```

## 使用
引用 注册 `from` 组件
```js
import BasicForm from '@/components/BasicForm';

components: {
  BasicForm,
},
```
在模板中使用
```html
<BasicForm
  v-loading="loading"
  element-loading-text="拼命加载中..."
  element-loading-background="#FFFFFF"
  :ref-obj.sync="formInfo.ref"
  :data="formInfo.data"
  :rules="formInfo.rules"
  :fieldList="formInfo.fieldList"
  :selectOption="formInfo.selectOption"
  count="3"
  labelWidth="150px"
  @handleEvent="handleEvent"
/>
```

## 数据结构
组件 `json` 数据格式,为了不混淆统一在 `formInfo` 这个大对象里
```js
formInfo: {
  ref: null,
  data: {},
  selectOption: {},
  fieldList: [],
  rules: {}
}
```
- `ref` 组件实例
- `data` 表单数据
- `selectOption` 下拉列表对象，里面存放下拉框数组，有几个就放几个（后端返回的下拉列表数据）
- `fieldList` 字段类型、label列表
- `rules` 表单验证规则

如果 `fieldList` 列表里 `type` 类型是 `select`，则 `list` 字段的 `value` 就是 `selectOption` 对象所对应的数组列表

```js
fieldList: [
  { label: '通道选择', type: 'select', value: 'passId', list: 'passOptions', hide: false },
]
```

## 数据结构示例
>关于表单校验规则封装可以看上一篇文章: [el-from 表单校验规则封装](/element-ui-rules/)


```js
data() {
  return {
    formInfo: {
      /** 表单组件实例 */
      ref: null,
      /** 表单数据 */
      data: {
        id: '',
        name: '',
        code: '',
        passQuantity: '',
        ipAddress: '',
        macAddress: '',
        model: '',
        manufacturer: '',
        location: '',
        backupOften: ''
      },
      /** 下拉列表参数 */
      selectOption: {
        passOptions: [],
        categoryOptions: [],
      },
      /** 字段类型、label列表 */
      fieldList: [
        { label: '主机名', type: 'input', value: 'name' },
        { label: '通道选择', type: 'select', value: 'passId', list: 'passOptions', hide: false },
        { label: '传感器类型', type: 'select', value: 'categoryId', list: 'categoryOptions', hide: false },
        { label: '主机编号', type: 'input', value: 'code'},
        { label: '通道数量', type: 'input', value: 'passQuantity' },
        { label: 'IP地址', type: 'input', value: 'ipAddress' },
        { label: 'Mac地址', type: 'input', value: 'macAddress' },
        { label: '型号', type: 'input', value: 'model' },
        { label: '生产厂家', type: 'input', value: 'manufacturer' },
        { label: '存放地址', type: 'input', value: 'location' },
        { label: '备份频率', type: 'input', value: 'backupOften' },
      ],
      /** 表单规则 */
      rules: {
        name: vxRule(),
        categoryId: vxRule(true, null, 'change', '请选择传感器类型'),
        distance: vxRule(),
        code: vxRule(),
        passQuantity: vxRule(true, "Number"),
        ipAddress: vxRule(true, "IpAddress"),
        macAddress: vxRule(true, "MacAddress"),
        backupOften: vxRule(),
      },
    },
  }
},
```

## 事件函数
该组件对外暴露 `handleEvent()` 事件函数，可以操纵每个 `form-item`，用来自定义事件。
```js
handleEvent (row) {
  console.log('表单绑定相关事件:',row);   // 表单绑定相关事件row: {value: "安徽蓝海之光模拟巷道", label: "location"}
},
```

## 结语
> 无论是十五岁，三十岁，四十岁，抑或五十岁，人们都为同样的事愤怒，为同样的事欢笑振奋；同样狡猾，同样软弱，卑微
