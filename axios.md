## axios

## axios特性

+ 从浏览器中创建 `XMLHttpRequests`
+ 从 `node.js` 创建 `http` 请求
+ 支持 `Promise` API
+ 拦截请求和响应
+ 转换请求数据和响应数据
+ 取消请求
+ 自动转换 JSON 数据
+ 客户端支持防御 `XSRF`(跨站请求伪造)

### axios安装

使用 npm:

``` html
npm install axios --save

import axios from "axios"

```
### axios中文文档(非官方)

https://www.kancloud.cn/yunye/axios/234845

### axios 官方文档地址(英文)

https://github.com/axios/axios

### axios返回的数据

+ config 请求的时候附带的配置参数
+ data 后端返回的数据
+ headers  请求头 里面包含发送给后端的格式 `application/json;charset=UTF-8`
+ request  ajax请求
+ status  返回的状态码
+ statusText  返回的状态文字

### 不凡电影接口  豆瓣电影图片做防盗链处理 需要加上这个<meta name="referrer" content="never">

+ 服务器地址 
  http://59.110.138.169

http://114.55.243.6:8080

+ 豆瓣电影接口
  https://apizza.net/pro/#/project/219bd7da9f36824337b05a33e9e08c51/browse?pass=a511db3cf920386c952dd6bdfbcf42fd

### 常用get/post请求

### axios get请求

+ 直接请求数据  
  `axios.get("http://59.110.138.169/api/douban/movie/in_theaters")`

+ get请求附带参数

```html
<script>
import axios from "axios" ;
export default {
  data () {
    return {
      list:""
    }
  },
  methods: {
    init(){ // 直接请求
      axios.get("http://59.110.138.169/api/douban/movie/in_theaters")
      .then(res =>{
        console.log(res);
        this.list = res.data.data;
      })
    },
    getPage(){ // 请求的时候附带参数
       axios.get("http://59.110.138.169/api/douban/movie/in_theaters",{
         params:{
           start:1,
           limit:5
         }
       })
      .then(res =>{
        console.log(res);
        this.list = res.data.data;
      })
    },
    getPage1(){ // 请求的时候附带参数
       axios.get("http://59.110.138.169/api/douban/movie/in_theaters?start=2&limit=3")
      .then(res =>{
        console.log(res);
        this.list = res.data.data;
      })
    }
  },
  created () {
    this.getPage1();
  }
}
</script>
```

+ post请求 
```html
<script>
// 请求的时候不用qs.stringify进行转换
register(){
    axios.post("http://114.55.243.6:8080/user/login",{
        loginType:0,
        mobile:18311111111,
        pwd:1,
        verifyCode:""
    })
    .then(res =>{
        console.log(res)
    })
}
// 请求的时候需要qs.stringify进行转换
register(){
    var obj = {
    username:"admin1231",
    password:"123",
    name:"张三",
    age:40,
    sex:0,
    avatar:"",
    school:"不凡",
    address:"郑州",
    }
    
    axios.post("http://59.110.138.169/api/test/user/save",qs.stringify(obj))
    .then(res =>{
    console.log(res)
    })
}
// 如果后端要求请求头类型是application/x-www-form-urlencoded 需要转换一下 如果是application/json 不需要用qs模块
</script>
```
### axios执行多个并发请求

一次执行多次ajax请求
promise.all
执行多个并发请求是通过`axios.all`完成  
`axios.all([function , function , ...])`
请求完成之后 会以数组的形式返回全部的请求数据
<script>
export default {
  methods: {
    init(){
      return axios.get('http://localhost:3000/')
     
    },
    getUserInfo(){
       return axios.post('http://localhost:3000/user',params )
      
    }
  },
  created () {
    axios.all([this.init(), this.getUserInfo()])
    .then((val)=>{  // val返回的是全部的请求
        console.log(val)
    });
  }
</script>

### 封装常用的get/post请求

> 前言
> get请求和post请求传参的写法

+ get请求传参

``` html
<script>
    axios.get('/user?ID=12345')
    axios.get('/user', {
        params: {
            ID: 12345
        }
    })
</script>

```

+ post请求传参的方式

```html
<script>
    axios.post('/user', {
        ID: 12345
    })
</script>

```

在src目录新建文件夹api
在api文件夹新建index.js

``` html
<script>
import axios from 'axios'
import { Loading } from 'element-ui'
import qs from "qs"

const service = axios.create({
  baseURL: "http://localhost:3000", // api的base_url
  timeout: 5000 // request timeout
});
let loadings;

// request interceptor
service.interceptors.request.use(config => {
//   var token = sessionStorage.getItem("token")
//   if (token) {
//     // 让每个请求携带token-- ['X-Token']为自定义key 请根据实际情况自行修改
//     config.headers['X-Token'] = token
//   }
  if(config.method == "post"){
    // config.headers['Content-Type'] = 'application/x-www-form-urlencoded';
    config.data = qs.stringify(config.data)
  }
  loadings = Loading.service({ fullscreen: true });
  return config
}, error => {
  
  console.log(error)
  Promise.reject(error)
})

// respone interceptor
service.interceptors.response.use(
  response => {
    loadings.close();
    return response
  },
  /**
   * 下面的注释为通过在response里，自定义code来标示请求状态
   * 当code返回如下情况则说明权限有问题，登出并返回到登录页
   * 如想通过xmlhttprequest来状态码标识 逻辑可写在下面error中
   * 以下代码均为样例，请结合自生需求加以修改，若不需要，则可删除
   */
  // response => {
  //   const res = response.data
  //   if (res.code !== 20000) {
  //     Message({
  //       message: res.message,
  //       type: 'error',
  //       duration: 5 * 1000
  //     })
  //     // 50008:非法的token; 50012:其他客户端登录了;  50014:Token 过期了;
  //     if (res.code === 50008 || res.code === 50012 || res.code === 50014) {
  //       // 请自行在引入 MessageBox
  //       // import { Message, MessageBox } from 'element-ui'
  //       MessageBox.confirm('你已被登出，可以取消继续留在该页面，或者重新登录', '确定登出', {
  //         confirmButtonText: '重新登录',
  //         cancelButtonText: '取消',
  //         type: 'warning'
  //       }).then(() => {
  //           location.reload() // 为了重新实例化vue-router对象 避免bug
  //       })
  //     }
  //     return Promise.reject('error')
  //   } else {
  //     return response.data
  //   }
  // },
  error => {
    console.log('err' + error) // for debug
    loadings.close();
    return Promise.reject(error)
  })

export default service
</script>
```

调用的时候先引入`api/index.js`
也可以把它挂载到vue原型上

``` html
<script>
    import api from "@/api"
    Vue.prototype.$ajax = api;
</script>

```

调用的时候执行

```html
<script>
this.$ajax.post('/user',datas  ).then(res=>console.log(res))
</script>
```

