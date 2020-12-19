### Vue 定义全局的公用的组件 及 局部组件

#### 1. 定义一个模板 如：header.vue  
```` html
<!-- /src/components/common/header.vue  -->
<template>
<div>
  <mu-appbar style="width: 100%;" color="primary">
    <mu-button icon slot="left">
      <mu-icon value="menu"></mu-icon>
    </mu-button>
    Title
    <mu-menu slot="right">
      <mu-button flat>MENU</mu-button>
      <mu-list slot="content">
        <mu-list-item button>
          <mu-list-item-content>
            <mu-list-item-title>Menu Item 1</mu-list-item-title>
          </mu-list-item-content>
        </mu-list-item>
        <mu-list-item button>
          <mu-list-item-content>
            <mu-list-item-title>Menu Item 2</mu-list-item-title>
          </mu-list-item-content>
        </mu-list-item>
      </mu-list>
    </mu-menu>
  </mu-appbar>
</div>
</template>

````

#### 2.  在main.js中引入文件  并声明为全局主件: header-view

```` javascript

<!-- /src/main.js  -->
import header from './components/common/header'
Vue.component('header-view', header)  

````

#### 3. 在需要的使用的vue页面中　写入该标签： <header-view></header-view>

```html

<!-- /src/components/Home.vue  -->
<template>
    <mu-container style="max-width: 400px; width:100%;">
    <header-view></header-view>  <!--引入标签 将渲染header.vur-->
    </mu-container>
</template>

```


#### 4.错误实例：

##### 4.1

> Failed to mount component: template or render function not defined.

######4.1.1原因:所定义的  component 的顶级标签不是 <template></template>

````html

<template>   <!-- 定义  component组件 时，component顶级标签必须是  <template></template> -->
<div class="header-row-fixed">
  ...
</div>
</template>

````

*****************************************


###  局部组件


#### 1.定义子组件模板：

```html
<template>
  <div>
    <el-dialog v-el-drag-dialog title="添加管理员" :visible.sync="addAdminDialogVisible" :fullscreen="false" class="add-admin-dialog" center>
      <el-row :gutter="24">
        <el-form ref="form" label-width="80px">
          <el-form-item label="用户名">
            <el-input v-model="adminobj.username" placeholder="管理员用户名" />
          </el-form-item>
        </el-form>
      </el-row>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: 'AddAdmin',
  props: ['adminobj', 'addAdminDialogVisible'],
  data() {
    return {
    }
  },
  methods: {
    addAdmin() {
      console.log(888)
    }
  }
}
</script>


<style lang="scss" scoped>

</style>

```

#### 1.1 要点 

>  template 单入口  `<template><div> div 元素为入口  </div></template>`  ;

>  定义参数 name   `name: 'AddAdmin'`,

>  外部传入的 props  `props: ['adminobj', 'addAdminDialogVisible']` string 类型,



### 2 父组件 引用


```html

<template>
  <div class="add-container">
    <AddAdmin 
    :add-admin-dialog-visible="addAdminDialogVisible" 
    :adminobj="adminObj">
    </AddAdmin>                         <!--3.  插入到父组件，传递绑定 必要的参数      -->
  </div>
</template>
<script>
import AddAdmin from './add.vue'        /* 1. 引入子组件*/
export default {
  name: 'AdminList',
  components: {
    'AddAdmin': AddAdmin                /* 2. 挂载 命名  到 components */
  },
  data() {
    return {
      addAdminDialogVisible: false,     /*子组件 必要的参数*/
      adminObj: {                       /*子组件 必要的参数*/
        username: '',
        password: '',
        status: true,
        nickname: '',
        isAdministrator: true
      }
    }
  },
  methods: {

  }
}
</script>
<style>
  .block-page-row{
    position: fixed;
    bottom: 0;
  }
</style>

```



#### 2.错误实例


##### 2.1  子组件未命名  即子组件  `name`： 为空 

```
```








