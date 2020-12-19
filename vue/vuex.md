##vuex
###1. 使用vuex 
 
####安装:  npm install vuex --save

#### 初始化store.js，

>在src/store/store.js下面，初始化代码。
````php
import Vue from 'vue'
import Vuex from 'vuex'
Vue.use(Vuex)
export const store = new Vuex.Store({})
````