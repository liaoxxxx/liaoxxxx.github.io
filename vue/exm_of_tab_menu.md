###  tab 菜单实例

``` html

<template>
    <div id="register">
        <div class="merchant">
            <div class="merchant-title">
                <ul class="wl-flex-between">
                    <li @click="transTab(0)" :class="{active1:activeTabIndex === 0}">
                            工程商
                    </li>
                    <li @click="transTab(1)" :class="{active1:activeTabIndex === 1}">
                            工程材料
                    </li>
                    <li @click="transTab(2)" :class="{active1:activeTabIndex === 2}">
                          工程劳务
                    </li>
                    <li @click="transTab(3)" :class="{active1:activeTabIndex === 3}">
                         工程保险
                    </li>
                
                </ul>
            </div>



            <div class="merchant-content wl-mt-13" v-show="activeTabIndex===0">
                <h1>{{activeTabIndex}} </h1> 
            </div>

            <div class="merchant-content wl-mt-13" v-show="activeTabIndex===1">
                <h1>{{activeTabIndex}} </h1> 
            </div>

            <div class="merchant-content wl-mt-13" v-show="activeTabIndex===2">
                <h1>{{activeTabIndex}} </h1> 
            </div>

            <div class="merchant-content wl-mt-13" v-show="activeTabIndex===3">
               <h1>{{activeTabIndex}} </h1> 
            </div>



        </div>
    </div>

</template>

<script>
    export default {

        data() {
            return {
                activeTabIndex:0,
              
            }

        },
      
        methods: {
            
            transTab(index){
                this.activeTabIndex=index
            },

        },
        created() {


        }
    }

</script>

<style scoped>
     .active1{
         color: #0C7FF2 !important;
         border-bottom: 2px solid #0C7FF2 !important;
     }
</style>

```



###  选择多个 checkbox

``` html
<template>
    <div>
        <li v-for="(item ,index) in caseList">
            <router-link :to="{path:'/revise/details'}">
                <div class="my-case-img">
                   <img :src="item.case_thumb" alt="">
                </div>
                <div class="my-case-text">
                <div class="my-case-text-title xsy-ellipsis-1">{{item.case_title}}</div>
                    <div class="my-case-text-day">耗时:{{item.cosume_time}}天</div>
                </div>
            </router-link>
    
            <label class="choose-boxs" v-show="showMerchants">
                <input  v-model="checkedItem" :id="index" :value="item.id" name="caseIdList[]" type="checkbox"/>
            </label>
        </li>
        
        
        <div class="my-case-btn" v-show="showMerchants">
           <button @click="submitForm('ruleForm')">删除</button>
        </div>
        
    </div>
</template>
<script>
    export default {
        data() {
            return {
                caseList: [{
                    id: 1,
                    case_title: '加载中...',
                    case_images: '',
                    case_summary: '',
                    case_thumb: '',
                    cosume_time: '',
                    create_time: '',
                    enterprise_id: 1,
                    is_recommend: '',
                    status: '',
                    update_time: '',
                },
                {
                    id: 2,
                    case_title: '加载中...',
                    case_images: '',
                    case_summary: '',
                    case_thumb: '',
                    cosume_time: '',
                    create_time: '',
                    enterprise_id: 1,
                    is_recommend: '',
                    status: '',
                    update_time: '',
                }],
            }
        },
                
        methods: {
            submitForm(formName) {
                console.log(this.checkedItem);
            }
        }
</script>
```