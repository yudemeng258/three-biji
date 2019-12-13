### Nuxt环境搭建

使用nuxt的前提必须已经安装了vue的脚手架,nuxt的项目通过脚手架来进行创建
`npm install vue-cli -g`

1. [初始化nuxt的项目](https://zh.nuxtjs.org/guide/installation "初始化nuxt的项目")
   `npx create-nuxt-app <项目名>`
2. 启动服务  
   `npm run dev`
3. 查看项目  
   在浏览器输入 localhost:3000,可以看到结果

### nuxt 配置sass

 `cnpm install --save-dev  node-sass sass-loader`  
 安装这两个插件之后就可以在项目里面使用scss

### Nuxt常用配置项 

+ 配置全局CSS  
  在开发多页项目时，都会定义一个全局的CSS来初始化我们的页面渲染，比如重置样式.
  - 在assets文件夹新建css文件夹 在新建reset.css  

```html
<script>
//    /assets/css/reset.css
// 重置样式随意网上搜下就行
html{
    color:red;
}

// /nuxt.config.js
css:['~assets/css/reset.css'],
</script>
```

配置完成后就可以看到文字颜色变成红色

+ 配置全局插件  
  常用插件如swiper:  

```html
<script>
  //1. 安装swiper
  `cnpm install vue-awesome-swiper --save`
  //2. /plugins/swiper.js  
  //在plugins文件夹下新建swiper.js 并填入以下内容
  import Vue from "vue";
  if (process.browser) {
      const VueAwesomeSwiper = require('vue-awesome-swiper/dist/ssr')
      Vue.use(VueAwesomeSwiper)
  }
  //3. 在nuxt.config.js里面引入swiper  
  css: [
    'swiper/dist/css/swiper.css'
  ],
  /*
  ** Plugins to load before mounting the App
  */
  plugins: [
    // ssr: Boolean (默认为 true) 如果值为 false，该文件只会在客户端被打包引入。
    { src: '~/plugins/swiper.js', ssr: false },
  ],
  // 4. 服务端渲染的代码和spa的代码不一样
  参考 https://github.com/surmon-china/vue-awesome-swiper#ssr
  // 5. 愉快的开始swiper的开发
</script>
```
+ 配置全局插件  
  element-ui:

```html
<script>
  //1. 安装element-ui
  `cnpm install element-ui --save`
  //2. /plugins/element.js  
  //在plugins文件夹下新建element.js 并填入以下内容
   import Vue from 'vue';
   import ElementUI from 'element-ui';

   Vue.use(ElementUI);
  //3. 在nuxt.config.js里面引入element  
  css: [
     "element-ui/lib/theme-chalk/index.css"
  ],
  /*
  ** Plugins to load before mounting the App
  */
   plugins: [
   "@/plugins/element.js",
   ],
</script>
 ```
+ 配置全局插件  
  axios :

```html
<script>
  //1. 安装axios
  `cnpm install axios --save`
  //2. /plugins/axios.js  
  //在plugins文件夹下新建axios.js 并填入以下内容(axios封装)
 import axios from "axios";
   import qs from "qs";
   import Vue from "vue"
  // import { Loading } from 'element-ui';
var loadings;
if(process.env.NODE_ENV == "development"){
    axios.defaults.baseURL = "http://59.110.138.169";
}else{
    axios.defaults.baseURL = "";
}
axios.defaults.timeout = 5000;
// axios.defaults.headers.common['token'] = token

axios.interceptors.request.use(function (config) {
    // 在发起请求请做一些业务处理  
    // 如开启loading  对请求的参数做处理 添加token等
    // 例  在请求的时候开启elementui的loading

    // loadings = Loading.service();
  if(config.method.toLowerCase() == "post"){
      config.data = qs.stringify(config.data);
  }
    return config;
}, function (error) {
    // 对请求失败做处理
    return Promise.reject(error);
});
Vue.prototype.$ajax = axios
//3. 在nuxt.config.js里面引入axios 
  /*
  ** Plugins to load before mounting the App
  */
   plugins: [
   "@/plugins/axios.js"
   ]
//axios 在页面可直接import axios 使用,不需要再配置
</script>
 ```

### Nuxt的路由配置和参数传递 

### 跳转标签<nuxt-link> 

使用nuxt提供的组件进行页面间的跳转 

### 传递参数 
+ query传值  
  直接在地址栏后面拼接需要传递的参数
  `<nuxt-link to="/info?id=10">商品列表页</nuxt-link>`

```html
<template>
    <div>
        商品列表页
        <button @click="toInfo">点击跳转到详情页</button>
        <nuxt-link to="/info?id=10">商品列表页</nuxt-link>
        <nuxt-link :to="{path:'/info',query:{id:20}}">商品列表页1</nuxt-link>
    </div>
</template>

<script>
export default {
    
    methods: {
        toInfo(){
            this.$router.push({path:"/info",query:{id:30}})
        }
    },
};
</script>

```
### nuxt传值  

+ 动态路由传值  
因为在nuxt中没有路由配置文件,在nuxt中路由配置文件由nuxt自动生成，所以创建按关于nuxt的动态路由需要特殊的创建,在 Nuxt.js 里面定义带参数的动态路由，需要创建对应的以下划线作为前缀的 Vue 文件 或 目录。
在pages文件夹新建文件夹 在这个文件夹新建_id.vue  id就是传递的参数 

### 嵌套路由  

如果想要新建嵌套路由的话  新建abc.vue  在新建abc文件夹 子页面存放在abc文件夹里面

### 登陆鉴权

安装cookie插件(因为是服务端渲染 所以获取不到window,localstorage等) `npm install js-cookie --save`  

1. 登陆的时候在本地设置token 

<script>
import Cookie from "js-cookie" 
Cookie.set('token', 123)
</script>

2. 在middleware文件夹新建requireAuth.js 用于登陆权限判断

<script>
import cookie from 'js-cookie'
//第一种
// export default function(val){
//     console.log(val);
//     var token, path;
//     if(process.client) { //如果当前环境处于客户端
//         token = cookie.get('token')
//         path = val.route.path;
//     }
//     console.log(token)
// }
//第二种
export default function(val){
    console.log(val,Cookie.get("username"));
    if(val.route.path == "/"){ //如果当前环境处于客户端
       if(Cookie.get("username")){
       }else{
           val.redirect("/login");
       }
    }
}
</script>

4. 在nuxt.config.js文件中加上  

<script>
  router:{  // 路由配置
    middleware:"requireAuth" // 当每次路由跳转时都使用requireAuth文件
  }
</script>

### Nuxt的路由动画效果 

路由的动画效果，也叫作页面的更换效果。Nuxt.js提供两种方法为路由提供动画效果，一种是全局的，一种是针对单独页面制作。

+ 全局路由动画  
  全局动画默认使用page来进行设置，例如现在我们为每个页面都设置一个进入和退出时的渐隐渐现的效果。我们可以先在根目录的assets/css下建立一个main.css文件。  
  /assets/css/main.css(没有请自行建立)

```html
.page-enter-active, .page-leave-active {
    transition: opacity 2s;
}
.page-enter, .page-leave-active {
    opacity: 0;
}
```

然后在nuxt.config.js里加入一个全局的css文件就可以了。   

```html
css:['assets/css/main.css'],
```

这时候在页面切换的时候就会有2秒钟的动画切换效果了  

+ 单独设置页面动效
  想给一个页面单独设置特殊的效果时，我们只要在css里改变默认的page，然后在页面组件的配置中加入transition字段即可。例如，我们想给about页面加入一个字体放大然后缩小的效果，其他页面没有这个效果。  
  在全局样式assets/main.css 中添加以下内容。

```html
.test-enter-active, .test-leave-active {
    transition: all 2s;
    font-size:12px;
    
}
.test-enter, .test-leave-active {
    opacity: 0;
    font-size:40px;
}
```

然后在about/index.vue组件中设置  

```html
export default {
  transition:'test'
}
```

这时候就有了页面的切换独特动效了。

### Nuxt的错误页面和个性meta设置 

当用户输入路由错误的时候，我们需要给他一个明确的指引，所以说在应用程序开发中404页面是必不可少的。Nuxt.js支持直接在默认布局文件夹里建立错误页面。  
在根目录下的layouts文件夹下建立一个error.vue文件，它相当于一个显示应用错误的组件。

```html
<template>
    <div>
    您输入的路径有问题
    <nuxt-link to="/">返回首页</nuxt-link>
    </div>
</template>
```
### 个性meta设置
 
//独立设置head信息
  head(){
      return{
        title:this.title, //自定义
        meta:[
          {content:'This is news page'}//自定义
        ]
      }
    }
}

### asyncData方法获取数据

<script>
import axios from "axios"
export default {
   // asyncData 是在服务器调取数据 当服务器访问页面的时候 这个数据是已经请求好的数据
    // created  是在浏览器环境请求数据  
    // asyncData 返回数据 
    async asyncData({ params }){
       var datas = await axios.get(`http://59.110.138.169/api/douban/movie/subject/${params.id}`)
       console.log(datas,123)
       return { 
           list:datas.data
       },
    head(){
      return{
        title:this.list.title,
        meta:[
          {content:this.list.summary}
        ]
      }
    },
    data() {
        return {
            name:"张三"
        };
    },
</script>

### 打包及预览

+ 静态服务部署
  Nuxt.js 可依据路由配置将应用静态化，使得我们可以将应用部署至任何一个静态站点主机服务商。
  `服务端渲染应用部署`:是每次请求经过服务器，查询数据库或接口，渲染模板后返回html，算是动态渲染的；
  `静态部署`: 是预先将所有路由页面处理后，生成静态的html，是一个完全不需要服务器的静态网站了。
+ 服务端渲染

1. 对应用进行打包 执行`npm run build`命令
2. 复制项目目录里面的.nuxt文件夹、static文件夹、nuxt.config.js、package.json到服务器中  
3. 安装项目依赖 npm install 
4. 运行 npm run start 项目就运行成功

+ 静态部署  
  静态部署直接执行一个命令 `npm run generate`
  *注意*  
  在 Nuxt.js 执行 generate 命令时，动态路由 会被忽略。 也就是说项目里面如果有动态路由 那么打包的时候就会忽略动态路由   
  [解决静态部署动态路由问题](https://zh.nuxtjs.org/api/configuration-generate#routes)
   在nuxt.config.js中设置
   generate: {
  // generate  通过axios  会找到所有的动态路由数据  同时把所有的数据打包成页面
  routes () {
    return axios.get('http://59.110.138.169/api/douban/movie/in_theaters')
      .then((res) => {
        console.log(res.data);
        return res.data.data.map((ele) => {
          return '/info/' + ele.id
        })
      })
  }
},