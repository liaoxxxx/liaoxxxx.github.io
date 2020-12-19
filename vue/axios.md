##  关于 Axios  的使用

###配置

```javascript
/*/src/util/request.js'*/
import axios from 'axios'
import store from '@/store'
import * as qs from 'qs'
const request = axios.create({
  baseURL: 'http://localhost:8081', //配置请求的url
  timeout: 5000, // 请求超时
  headers: { 'Content-Type': 'application/x-www-form-urlencoded' }
})

// 请求 拦截器
request.interceptors.request.use(
  config => {
    // do something before request is sent
    // 每次请求带上   token
    if (store.getters.token) {             
      // let each request carry token
      // ['X-Token'] is a custom headers key
      // please modify it according to the actual situation
      // config.withCredentials = true
    }

    //post请求配置
    config.method === 'post'
      ? config.data = qs.stringify({ ...config.data })
      : config.params = { ...config.params }
    return config
  },
  //请求出错的配置
  error => {
    // do something with request error
    console.log(error) // for debug
    return Promise.reject(error)
  }
)
export default request
```

### 挂载到 $request ： Vue.prototype.$request

````javascript

import request from './util/request'
Vue.prototype.$request = request
new Vue({
  el: '#app',
  router,
  components: { App },
  template: '<App/>'
})

`````

### 使用

```javascript
	this.$request.get('/goodsapi/recommendList').then(function (response) {
		//dosomething
	})
    .catch(function (error) {
        // handle error
    })
```





###1. 
```javascript

 baseURL: 'http://localhost:8081',

 headers: { 'Content-Type': 'application/x-www-form-urlencoded' }
 
``` 

#### baseURL :用于开发环境中访问 后端,避免后端端口不一致带来的问题 

#### headers: { 'Content-Type': 'application/x-www-form-urlencoded' }  把Content-Type 设为 :'application/x-www-form-urlencoded'  为了 提交的数据格式变成 Request Payload 导致  spring boot 后端服务 中解析到 DTO 对象需要@RequestBopdy 注解  ,所以设为:application/x-www-form-urlencoded,使提交的数据格式为Form Data  


###2.
```javascript
 //post请求配置
    config.method === 'post'
      ? config.data = qs.stringify({ ...config.data })
      : config.params = { ...config.params }
```
####由于 [1] 中headers: { 'Content-Type': 'application/x-www-form-urlencoded' }  把Content-Type 设为 :'application/x-www-form-urlencoded'  数据提交的格式为 Form Data,所以要将提交的数据使用 qs.stringify(Object)进行json编码