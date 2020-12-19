####路由

#### 1. this.$router 与 this.$route 的区别 

> 通过注入路由器，我们可以在任何组件内通过 this.$router 访问路由器，也可以通过 this.$route 访问当前路由


> 可以理解为：


> this.$router 相当于一个全局的路由器对象，包含了很多属性和对象（比如 history 对象），任何页面都可以调用其 push(), replace(), go() 等方法。


> this.$route 表示当前路由对象，每一个路由都会有一个 route 对象，是一个局部的对象，可以获取对应的 name, path, params, query 等属性。



#### 2.使用
##### 2.1 定义路由  /src/router/index.js
````javascript
import Vue from 'vue'
import Router from 'vue-router'
import Home from '@/components/Home'
import Goods from '../components/Goods'
import confirmOrder from '../components/confirmOrder'
Vue.use(Router)
export default new Router({
  routes: [
    {
      path: '/',		//   url 路径   
      name: 'home',		//	 name 用于 $router 传递 name参数
      component: Home   //   映射  /src/component/Home.vue 组件
    },
    {
      path: '/goods',
      name: 'goods',
      component: Goods
    },
    {
      path: '/confirmOrder',
      name: 'confirmOrder',
      component: confirmOrder
    }
  ]
})

````
##### 2.2 通过 格式：{name:name,params:{}}跳转  this.$router | $route.push({name:name,params:{}})   
````javascript

 this.$router.push({
	name: 'goods',     //已经在routes[]定义的 name参数 
	params: { 
		goodsId: goodsId   //传递的参数
	}
 })
 
this.$route.push({
	name: 'goods', 
    params: { 
		goodsId: goodsId 
	}
})

````


> params 传参，push 里面只能是 name:'xxx'，不能是 path:'/xxx'，因为 params 只能用 name 来引入路由，如果这里写成了 path ，接收参数页面会是 undefined；


##### 2.3 在 Goods.vue 获取传递过来的参数,参数从 this.$router.params 中获取

```javascript
	console.table(this.$router.params)
	const goodsId =this.$router.params.goodsId;  
```


##### 2.4 通过格式：{path:'path',query:{}}跳转  this.$router | $route.push({path:'path',params:{}})   

````javascript
this.$router.push({
    path: 'goods',  			//已经在routes[]定义的 path参数 
    query: { 
   		goodsId: goodsId		//传递的参数
    }
})
this.$router.push({
    path: 'goods', 
    query: { 
   		goodsId: goodsId
    }
})
````


> 将会在urL 显式 传递参数  


````text
	http://localhost:8088/#/goods?goodsId=33
````



##### 2.5 在 Goods.vue 获取传递过来的参数,参数从 this.$router.query 中获取

```javascript
	console.table(this.$router.query)
	const goodsId =this.$router.query.goodsId;  
```