---
title: 团队开发-代码风格指南
tags: [代码风格指南, 团队开发]
categories: [StyleGuide]
date: 2024-7-15 23:40:47
cover: https://03c068f.webp.li/i/2025/02/08/wfzz7-zr.jpg
---

##  .vue 文件行数规范

一般来说，一个 `.vue` 文件行数建议不超过 **`400`** 行，超过建议组件化拆分

##  变量命名

引用类型使用 `const` 定义是因为 `const` 声明的变量不能被重新赋值，但其内容是可以改变的。这样可以防止变量被意外重新赋值，从而提高代码的稳定性和可读性。

```js
<script setup lang="ts">
// 一般情况下，引用类型使用 const 定义，基本类型使用 let 定义
const arr = []
const obj = {}
const fn = () => {
  console.log('123')
}

let num = 10
let str = 'abc'
let flag = false

// vue3 中 ref、reactive 返回的是一个引用类型
const loading = ref(false)
const person = reactive({ name: '张三', age: 20 })
</script>
```

```js
<script setup lang="ts">
const loading = ref(false) // 加载
const visible = ref(false) // 显示隐藏
const showAddModal = ref(false) // 新增功能的模态框显示隐藏
const showAddDrawer = ref(false) // 新增功能的抽屉显示隐藏

// 或者 是否显示弹窗(是否的需要加 is 前缀)
const isShowModal = ref<boolean>(false)
const isLogin = ref(false) // 是否登录
const isVIP = ref(false) // 是否是VIP用户
const isDisabled = ref(true) // 是否被禁用

// 表单不建议 formData，直接最简
const form = reactive({
  name: '',
  phone: '',
  remark: ''
})

const userInfo = ref({}) // 用户信息
const tableData = ref([]) // 表格数据
const treeData = ref([]) // 树结构数据

// 对象数组 列表数据后面加 List
const companyList = ref([])
const checkedList = ref([])
const selectedList = ref([])
const addressList = ref([])
const userList = [
  { id: '01', name: '张三' },
  { id: '02', name: '李四' }
]
const tableData = []
const optionsList = [
  { label: '哈哈', value: 1 },
  { label: '嘻嘻', value: 2 }
]

// 非对象数组 在字母后面加s
const ids = []
const selectedIds = []
const activatedKeys = []
const nums = [3, 5, 6]
const strs = ['aaa', 'bbb', 'ccc']

// bad
// function getData() {
//   const arr = []
//   nums.forEach((item) => {
//     arr.push({ value: item })
//   })
// 	 return arr
// }

// good
function getData() {
  return nums.map(item => ({ value: item }))
}

async function getUserList() {
  try {
    const res = await Api.getUserPage()
    userList = res.data
  } catch (error) {
    console.error('Failed to fetch user list:', error)
  }
}

// ---------------------------------------- 方法 --------------------------------------------- //

// 编辑
function handleEdit() {}

// 新增
function handleAdd() {}

// 删除
// function delete() {} // 禁止，delete 是JS关键词
function handleDelete() {}

// 重命名
function handleRename() {}

// 批量删除
function handleMulDelete() {}

// 搜索
function handleSearch() {}

// 返回
function handleBack() {}

// 提交
function handleSubmit() {}

// 确认
function handleConfirm() {}
function handleOk() {}

// 取消
function handleCancel() {}

// 打开 | 关闭
function handleOpen() {}
function handleClose() {}

// 保存
function handleSave() {}

// 获取表格列表
function getTableData() {}
function getTableList() {}
</script>
```

## **常用前缀**

| 前缀       | 前缀 + 命名                  | 含义                        |
| ---------- | ---------------------------- | --------------------------- |
| get        | getUserInfo                  | 获取用户信息                |
| del/delete | delUserInfo                  | 删除用户信息                |
| update/add | updateUserInfo / addUserInfo | 修改用户信息 / 增加用户信息 |
| is         | isTimeout                    | 是否超时                    |
| has        | hasUserInfo                  | 有没有用户信息              |
| handle     | handleLogin                  | 处理登录                    |
| calc       | calcAverageSpeed             | 计算平均速度                |

##  vue 相关的命名

```js
<script setup lang="ts">
const isEdit = ref(false)

// bad
const title = computed(() => {
  return isEdit.value ? '编辑' : '新增'
})

// good 能一行就尽量写一行 注：三目仅限于单条件判断，禁止嵌套三目
const title = computed(() => (isEdit.value ? '编辑' : '新增'))
</script>
```

```js
<script setup lang="ts">
// 表单建议使用 form 命名(简洁)，不必使用 formData, 同时使用 reactive
const form = reactive({
  name: '',
  phone: ''
})
</script>
```

```js
<script setup lang="ts">
// 如果有重置 form 的需求，可以使用函数来返回一个新的初始对象，确保每次调用时都得到一个独立的对象
function getInitForm() {
  return {
    name: '',
    phone: '',
    email: '',
    sex: 1,
    age: '',
  }
}

const form = reactive(getInitForm())

// 重置form
function resetForm() {
  for (const key in form) {
    delete form[key]
  }
  Object.assign(form, getInitForm())
}
</script>
```

```js
<script setup lang="ts">
import { useAppStore, useUserStore } from '@/stores'
import { useLoading } from '@/hooks'

// stores 或 hooks 的使用命名规则定义
const appStore = useAppStore()
const userStore = useUserStore()

const { loading, setLoading } = useLoading()
</script>
```

##  写法技巧

尽量使用三目表达式

```js
// bad
let isEdit = true
let title = ''
if (isEdit) {
  title = '编辑'
} else {
  title = '新增'
}

// good
let title = isEdit ? '编辑' : '新增'
```

善用 includes 方法

```js
// bad
if (type === 1 || type === 2 || type === 3) {
}

// good
if ([1, 2, 3].includes(type)) {
}
```

使用箭头函数简化函数

```js
// bad
function add(num1, num2) {
  return num1 + num2
}

// good
const add = (num1, num2) => num1 + num2
```

```js
// 比例进度条颜色 尽量减少 if else if
function getProportionColor(proportion) {
  if (proportion < 30) {
    return 'danger'
  }
  if (proportion < 60) {
    return 'warning'
  }
  return 'success'
}

```

```js
// bad
const status = 200
const message = ''
if (status === 200) {
  message = '请求成功'
} else if (status === 404) {
  message = '请求出错'
} else if (status === 500) {
  message = '服务器错误'
}

// good：使用映射对象（messageMap）来简化条件判断的方式
// 注：仅适用于简单的状态映射场景

// 这种方法的优点包括：
// 简洁：代码更加简洁，避免了冗长的条件判断。
// 可维护：添加或修改状态码与消息的映射关系更加方便，只需修改 messageMap 对象即可。
// 性能：查表法在性能上通常优于多重条件判断，尤其是在状态码较多的情况下。
const status = 200
const messageMap = {
  200: '请求成功',
  404: '请求出错',
  500: '服务器错误'
}
const message = messageMap[status]
```

如果函数参数超过两个务必使用对象传参

```js
<script setup lang="ts">
function createUser(name, phone, age) {
  console.log('姓名', name)
  console.log('手机', phone)
  console.log('年龄', age)
}

// 这种方式在使用的时候可读性很差，扩展性差，而且不易于维护
createUser('张三', '178****2828', 20)

function createUser2({ name, phone, age }) {
  console.log('姓名', name)
  console.log('手机', phone)
  console.log('年龄', age)
}

// 以对象传参更直观，更好扩展和维护
createUser2({ name: '张三', phone: '178****2828', age: 20 })
</script>
```

##  接口 api 的命名

命名规范： 操作名 + 后端模块名 + 功能名

```js
import type * as T from './type'
import http from '@/utils/http'

/** 获取用户列表 */
export function getUserList() {
  return http.get<PageRes<T.UserItem[]>>('/user/list')
}

/** 获取用户详情 */
export function getUserDetail() {
  return http.get<T.UserDetail>('/user/detail')
}

/** 新增用户 */
export function addUser(data: any) {
  return http.post<T.UserAddResult>('/user/add', data)
}

/** 编辑用户 */
export function updateUser(data: any) {
  return http.post<T.UserUpdateResult>('/user/update', data)
}

/** 删除用户 */
export function deleteUser(data: { id: string }) {
  return http.post<T.UserDeleteResult>('/user/delete', data)
}
```

##  正则使用示例

文件位置：@/utils/regexp.ts

```js
/** @desc 正则-手机号码 */
export const Phone = /^1[3-9]\d{9}$/

/** @desc 正则-邮箱 */
export const Email = /^\w+([-+.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*$/

/** @desc 正则-6位数字验证码正则 */
export const Code_6 = /^\d{6}$/

/** @desc 正则-4位数字验证码正则 */
export const Code_4 = /^\d{4}$/

/** @desc 正则-16进颜色值 #333 #8c8c8c */
export const ColorRegex = /^#?([a-fA-F0-9]{6}|[a-fA-F0-9]{3})$/

/** @desc 正则-只能是中文 */
export const OnlyCh = /^[\u4e00-\u9fa5]+$/gi

/** @desc 正则-只能是英文 */
export const OnlyEn = /^[a-zA-Z]*$/

/** @desc 登录注册-密码 6-16位大小写字母、数字的js正则 */
export const Password = /^[a-zA-Z0-9]{6,16}$/
```

使用

```js
<script lang="ts" setup>
import { reactive } from 'vue'
import { Message } from '@arco-design/web-vue'
// 正则推荐一下导入方式
import * as Regexp from '@/utils/regexp'

const form = reactive({
  name: '',
  phone: ''
})

function handleSubmit() {
  if (!Regexp.Phone.test(form.phone)) {
    return Message.warning('请输入正确手机号格式')
  }
}
</script>
```

页面模板 CSS 类名采用半角连接符(-)

```html
<template>
  <div class="detail">
    <h3 class="title">标题</h3>
    <section class="table-box">
      <table></table>
    </section>
  </div>
</template>
```

##  全局组件-命名规范

[Vue3 官网-风格指南](https://vuejs.org/style-guide/)

组件命名：**单文件组件的文件名要全驼峰 (PascalCase)**

```
Title.vue
ThemeBtn.vue
SvgIcon.vue
```

##  弹窗组件 Modal、抽屉组件 Drawer 的一般封装

```html
<template>
  <a-modal v-model:visible="visible" :title="title" @ok="confirm">
    <!-- 内容 -->
  </a-modal>
</template>

<script setup lang="ts">
import { computed, reactive, ref } from 'vue'

const visible = ref(false)
const detailId = ref('')
const isEdit = computed(() => !!detailId.value) // 判断是新增还是编辑模式
const title = computed(() => (isEdit.value ? '编辑' : '新增'))

const add = () => {
  detailId.value = ''
  visible.value = true
}

// 如果这里的参数超过两个，优化成对象形式
// const edit = ({ id, taskId }) = {
//   console.log(id, taskId)
// }

const edit = (id: string) => {
  detailId.value = id
  visible.value = true
  // getDetail() 回显操作
}

defineExpose({ add, edit })

const confirm = () => {
  console.log('点击了确认按钮')
}
</script>
```

使用

**`模板里使用自定义组件：使用大写开头驼峰，双击好复制，对于搜索便利`**

```html
<template>
  <EditModal ref="EditModalRef"></EditModal>
</template>

<script setup lang="ts">
import EditModal from './EditModal.vue'

const EditModalRef = ref<InstanceType<typeof EditModal>>()

// 新增
const handleAdd = () => {
  EditModalRef.value?.add()
}

// 编辑
const handleEdit = (item: PersonItem) => {
  EditModalRef.value?.edit(item.id)
}
</script>
```

##  组件使用建议

按钮、元素之间间隔尽量使用 **Space** 组件或者使用原子样式（推荐）

```html
<template>
  <a-space :size="10">
    <a-button>返回</a-button>
    <a-button type="primary">提交</a-button>
  </a-space>
</template>

 <!-- 原子样式：我们可以仅在父级上使用space-x-*, space-y-*属性，而不是在每个子级上使用gap, margin, padding属性。 -->
<ul class="flex flex-row space-x-5">
  <li class="size-16 bg-red-400">Item 1</li>
  <li class="size-16 bg-red-500">Item 2</li>
  <li class="size-16 bg-red-600">Item 3</li>
</ul>
```

![Tailwind css, space between elements using space-x-5 class.](https://miro.medium.com/v2/resize:fit:1050/1*wTK_kErTmq9llSUAztEHKQ.png)

##  CSS 命名规范

使用小写连字符模式或者采用或者采用`BEM`命名规范 [BEM 命名规范](https://gitee.com/link?target=https%3A%2F%2Fgetbem.com%2Fnaming%2F)

```
// good
.header
.footer
.main
.content
.container
.page
.detail
.pane-left
.pane-right
.list
.list-item


// bad
.Header
.listItem
.list-Item
.List-Item;
```

**BEM 命名规范**

```html
<div class="article">
  <div class="article__body">
    <button class="article__button--primary"></button>
    <button class="article__button--success"></button>
  </div>
</div>

<style lang='sass' scoped>
.article {
  max-width: 1200px;
  &__body {
    padding: 20px;
  }
  &__button {
    padding: 5px 8px;
    &--primary {
      background: blue;
    }
    &--success {
      background: green;
    }
  }
}
</style>
```

##  CSS 的命名词汇

```
前一个    prev
后一个    next
当前的    current

显示的    show
隐藏的    hide
打开的    open
关闭的    close

选中的    selected
有效的    active
默认的    default
反转的    toggle

禁用的    disabled
危险的    danger
主要的    primary
成功的    success
提醒的    info
警告的    warning
出错的    error

大型的    lg
小型的    sm
超小的    xs
```

```
文档    doc
头部    header(hd)
主体    body
尾部    footer(ft)
主栏    main
侧栏    side
容器    box/container
```

```
列表    list
列表项  item
表格    table
表单    form
链接    link
标题    caption/heading/title
菜单    menu
集合    group
条      bar
内容    content
结果    result
```

```
按钮        button(btn)
下拉菜单    dropdown
工具栏      toolbar
分页        page
缩略图      thumbnail
警告框      alert
进度条      progress
导航条      navbar
导航        nav
子导航      sub-nav
面包屑      breadcrumb(crumb)
标签        label
徽章        badge
巨幕        jumbotron
面板        panel
洼地        well
标签页      tab
提示框      tooltip
弹出框      popover
轮播图      carousel
手风琴      collapse
定位浮标    affix
```

```
品牌        brand
标志        logo
额外部件    addon
版权        copyright
注册        register(reg)
登录        login
搜索        search
热点        hot
帮助        help
信息        info
提示        tips
开关        toggle
新闻        news
广告        advertise(ad)
排行        top
下载        download
```

```
左浮动    fl
右浮动    fr
清浮动    clear
```

## tailwind / unocss 使用技巧

### 轻松截断文本

另一个漂亮的实用程序类是`line-clamp` ，它允许您通过简单地添加一个数字（例如`line-clamp-3`来截断多行文本：

```html
<article class="mt-20 border border-slate-300 rounded-md p-4 ml-6 text-white w-60">
  <p class="line-clamp-3">
    Nulla dolor velit adipisicing duis excepteur esse in duis nostrud
    occaecat mollit incididunt deserunt sunt. Ut ut sunt laborum ex
    occaecat eu tempor labore enim adipisicing minim ad. Est in quis eu
    dolore occaecat excepteur fugiat dolore nisi aliqua fugiat enim ut
    cillum. Labore enim duis nostrud eu. Est ut eiusmod consequat irure
    quis deserunt ex. Enim laboris dolor magna pariatur. Dolor et ad sint
    voluptate sunt elit mollit officia ad enim sit consectetur enim.
  </p>
</article>
```

渲染结果将在 3 行文本后放置一个省略号：

![Untitled](https://cdn.builder.io/api/v1/image/assets%2FYJIGb4i01jvw0SRdL5Bt%2F67e62443191443c1b62a85e49f51302f?width=450)

### **元素之间的空间**

我们可以仅在父级上使用`space-x-*, space-y-*`属性，而不是在每个子级上使用`gap, margin, padding`属性。

```html
<ul class="flex flex-row space-x-5">
  <li class="size-16 bg-red-400">Item 1</li>
  <li class="size-16 bg-red-500">Item 2</li>
  <li class="size-16 bg-red-600">Item 3</li>
</ul>
```

![Tailwind css, space between elements using space-x-5 class.](https://miro.medium.com/v2/resize:fit:1050/1*wTK_kErTmq9llSUAztEHKQ.png)

### **组悬停**

```html
<button className="group">
  <span>Click!</span>
  <span className="ml-2 inline-block group-hover:rotate-90">
    &rarr;
  </span>
</button>
```

![Tailwind CSS, group hover example using button.](https://miro.medium.com/v2/resize:fit:900/1*qWF68_HWwl7hA1ar0Gxtgg.gif)

###  **自定义输入值**

很多时候，预定义的类名不足以完成这项工作。我们可能想要向预定义的顺风类添加自定义值。这可以通过使用`property-[value]`语法来实现。

```html
<h5 className="text-[#007bff] text-[4rem]">Hello World</h5>
```

