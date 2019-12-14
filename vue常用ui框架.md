## vue常用ui框架

pc端`element iview `
移动端 `vant cub

### pc端ui框架 element

Element，一套为开发者、设计师和产品经理准备的基于 Vue 2.0 的桌面端组件库。
它的开发团队是饿了么。

### element安装

`vue-cli3.x`安装参考下面地址
[vue3.x安装方法](https://github.com/ElementUI/vue-cli-plugin-elemen2t "vue3.x安装方法")

### npm 安装element ui框架  

+ `cnpm install  element-ui --save` 等价 `npm i element-ui -S`

### vue-cli的项目引入element的两种方式

`element-ui`安装后提供两种引入方式，全局引入以及按需引入，整个element-ui大概有1M左右，如果我们只需要几个组件(例如button)，这个时候全局引入就有些不合适了,按需引入可以很好的减少打包后的体积。

+ 全局引入 推荐
  把ui框架一次性的导入 
  - 好处 全部导入 节省了引用组件时的代码
  - 坏处 全部导入 有的组件用不到 就增加了项目的体积 

```html 
<!-- 全局引用的方式 -->
<!-- 在main.js中写入下列内容 -->
import ElementUI from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';
Vue.use(ElementUI);
```
+ 按需引入  

  把需要的组件引入 不需要的就不导入 打包项目的打包仅仅是引用的组件 大大减少了打包时的体积 
  坏处 每次引入的时候需要写一些额外的代码  

`npm install babel-plugin-component -D`

在babel.config.js里面加下面一段就可以
[按需引入参考](https://element.eleme.cn/2.10/#/zh-CN/component/quickstart "按需引入参考")

```html
"plugins": [
    [
      "component",
      {
        "libraryName": "element-ui",
        "styleLibraryName": "theme-chalk"
      }
    ]
  ]
```

## element开始

### Layout 布局

layout把页面分为24份，采用百分比布局
通过 row 和 col 组件，并通过 col 组件的 span 属性我们就可以自由地组合布局。

+ row组件
  row组件为容器组件 在这个组件里面可以设置子元素之间的间隔
  起作用的是给每个子组件设置内联样式`padding-left:10px;padding-right:10px;`

```html
<el-row :gutter="20"></el-row>
```

+ col组件 
  col组件是布局组件
  `<el-col :span="24"></el-col>` 这个el-col就占页面的`100%`,`<el-col :span="23"></el-col>`,这个el-col就占页面的`95.83333%`
+ 分栏偏移 
  元素偏移多少单位 通过制定 col 组件的 offset 属性可以指定分栏偏移的栏数。也是百分比偏移 `margin-left:25%;`

```html
<el-row :gutter="20">
  <el-col :span="6"><div class="grid-content bg-purple"></div></el-col>
  <el-col :span="6" :offset="6"><div class="grid-content bg-purple"></div></el-col>
</el-row>
```

### 响应式布局

element 预设了五个响应尺寸：xs、sm、md、lg 和 xl。采当用媒体查询 显示区域在某个范围内时使用某一个尺寸

+ mobile – xs ( <768px ) 如iphonex  750px
+ tablet – sm ( 768~991px )  ipad
+ desktop – md ( 992~1199px ) 老式的电脑 比如大学学校电脑正方体的屏幕
+ large desktop – lg ( 1200px~1900px ) 笔记本到台式电脑
+ xl  (>1900px ) 超大电脑屏幕
  如下面这段  在xs时占33.333%;在sm的时候占25%；在md时占16.666%；在lg时占12.5%；在xl时占4.16%；

```html
<el-row :gutter="10">
  <el-col :xs="8" :sm="6" :md="4" :lg="3" :xl="1"><div class="grid-content bg-purple"></div></el-col>
</el-row>
```

## element表单自定义验证

### 自定义表单验证步骤  

1.  在 el-form 表单里面添加需要校验的 规则(`rules`)  `<el-form ref="ruleForm" :model="ruleForm" label-width="80px" :rules="rules">`
2.  在表单的每一项 添加需要校验的属性 
    `<el-form-item label="用户名" prop="username"><el-input v-model="form.username"></el-input></el-form-item>`
    这一步需要检验的是 username  所以需要把这个username 添加到表单对应的这一项上面 同时 加个属性prop prop对应的值就是需要校验的属性
3. 书写自定义校验规则  `rules`  里面的username  pass 就是校验的每一项 对应prop

```html
<script>
    data(){
         var validatePass = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请输入密码'));
        } else {
          if (this.ruleForm.checkPass !== '') {
            this.$refs.ruleForm.validateField('checkPass');
          }
        }
      };
    return {
        ruleForm: {
          pass: '',
          checkPass: '',
          age: ''
        },
rules:{ // 校验规则
    username: [
      // validator 校验规则 trigger 表单类型的触发方式 blur
      { validator: validateusername, trigger: 'blur' }
    ],
    pass: [
      { validator: validatepass, trigger: 'blur' }
    ]
  }
 }
</script>
```

4. 实现校验规则 validator 的值就是校验规则 validateusername 这个函数需要自己手写 这个校验函数 写在data 里面的return 上面

5. 注意事项  validateusername 函数里面的callback  就是返回的校验信息  `return callback(new Error('密码长度不能小于6'));`  如果校验成功 什么都不返回 

### vant安装

[vant安装](https://youzan.github.io/vant/#/zh-CN/quickstart "vant安装")

+ `cnpm install  element-ui --save`

### vant引入的两种方式 

+ 全局引入

```html
<script>
//在main.js中
import Vue from 'vue';
import Vant from 'vant';
import 'vant/lib/index.css';

Vue.use(Vant);
</script>
```

+ 按需引入
  首先安装babel插件

> 注意：配置 babel-plugin-import 插件后将不允许导入所有组件 

```html
<script>
npm i babel-plugin-import -D
</script>
```

配置babel

```html
<script>
module.exports = {
  plugins: [
    ['import', {
      libraryName: 'vant',
      libraryDirectory: 'es',
      style: true
    }, 'vant']
  ]
};
</script>
```

用的时候直接引入需要的组件即可
如

```html
<template>
  <div>
    <van-button type="default">默认按钮</van-button>
  </div>
</template>
<script>
import { Button } from 'vant';
import Vue from "vue";
Vue.use(Button)
</script>
```

安装参考
[vant引入方式](https://youzan.github.io/vant/#/zh-CN/quickstart "安装参考")


### vant rem适配 

vant的px尺寸转rem参考以下网址
[vant的px尺寸转rem](https://www.cnblogs.com/lml2017/p/9953429.html "vant的px尺寸转rem");

- 1，安装 flexible和 postcss-px2rem（命令行安装）
npm install lib-flexible --save

npm install postcss-px2rem --save

- 2, 引入lib-flexible
在项目入口文件main.js 中引入lib-flexible

import 'lib-flexible'

- 3, 配置postcss-px2rem
px2rem的配置放在vue-cli3 项目中vue.config.js中（找不到？可能你是一个新构建的项目，需要手动在项目根目录创建vue.config.js）

具体配置内容如下：

复制代码
 1 module.exports = {
 2     css: {
 3         loaderOptions: {
 4           css: {},
 5           postcss: {
 6             plugins: [
 7               require('postcss-px2rem')({
 8                 remUnit: 37.5
 9               })
10             ]
11           }
12         }
13     },
14 }
复制代码
OK，重启项目，两个用于移动端适配的包就这样可以愉快的开始使用了！！！

### 上拉加载 

参考 这个网址  https://www.cnblogs.com/kerryw/p/9459621.html

### 下拉刷新 

vant的下拉刷新 使用前提首先样式得到位 

`.van-pull-refresh__track`是选择的时候可下拉的区域 所以得设置他的高度