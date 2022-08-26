<template>
<a-spin :spinning="confirmLoading">

   <a-tabs v-model="activeKey" @change="handleChangeTabs">
   <!--主表区域 -->
    <a-tab-pane tab="权限控制演示" :key="refKeys[0]" :forceRender="true" :class="'jeecg-tabs-top'" :animated="false">
         <a-form-model ref="form" :model="model" :rules="validatorRules">
           <a-row>
                  <a-col :xs="24" :sm="12">
                    <a-form-model-item label="部门" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="orgCode">
                      <j-select-depart v-model="model.orgCode" :multi="true"  />
                    </a-form-model-item>
                  </a-col>
                  <a-col :xs="24" :sm="12">
                    <a-form-model-item label="姓名" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="userName">
                      <a-input v-model="model.userName" placeholder="请输入姓名" ></a-input>
                    </a-form-model-item>
                  </a-col>
                  <a-col :xs="24" :sm="12">
                    <a-form-model-item label="年龄" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="userAge">
                      <a-input-number v-model="model.userAge" placeholder="请输入年龄" style="width: 100%" />
                    </a-form-model-item>
                  </a-col>

                </a-row>
              </a-form-model>

     </a-tab-pane>
   <!--子表单区域 -->
     <a-tab-pane tab="权限控制演示子表" :key="refKeys[1]" :forceRender="true">
       <j-editable-table
         :ref="refKeys[1]"
         :loading="authorityRuleTestSubTable.loading"
         :columns="authorityRuleTestSubTable.columns"
         :dataSource="authorityRuleTestSubTable.dataSource"
         :maxHeight="300"
         :rowNumber="true"
         :rowSelection="true"
         :actionButton="true"/>
     </a-tab-pane>

   </a-tabs>

 </a-spin>
</template>

<script>

import { FormTypes,getRefPromise } from '@/utils/JEditableTableUtil'
import { JEditableTableModelMixin } from '@/mixins/JEditableTableModelMixin'
import { validateDuplicateValue } from '@/utils/util'
import { VALIDATE_NO_PASSED, validateFormModelAndTables } from '@/utils/JEditableTableUtil'

export default {
 name: 'AuthorityRuleTestForml',
 mixins: [JEditableTableModelMixin],
 components: {
 },
 data() {
   return {
     labelCol: {
       xs: { span: 24 },
       sm: { span: 5 },
     },
     wrapperCol: {
       xs: { span: 24 },
       sm: { span: 16 },
     },
     // 新增时子表默认添加几行空数据
     addDefaultRowNum: 1,
     model:{
     },
        validatorRules: {
        },
     refKeys: ['authorityRuleTest','authorityRuleTestSub', ],
     tableKeys:['authorityRuleTestSub', ],
     activeKey: 'authorityRuleTest',
     // 权限控制演示子表
     authorityRuleTestSubTable: {
       loading: false,
       dataSource: [],
       columns: [
         {
           title: '薪资',
           key: 'salary',
           type: FormTypes.inputNumber,
           width:"200px",
           placeholder: '请输入${title}',
           defaultValue:'',
         },
         {
           title: '工龄',
           key: 'workYears',
           type: FormTypes.inputNumber,
           width:"200px",
           placeholder: '请输入${title}',
           defaultValue:'',
         },
         {
           title: '工号',
           key: 'jobNumber',
           type: FormTypes.input,
           width:"200px",
           placeholder: '请输入${title}',
           defaultValue:'',
         },
         {
           title: '发薪日期',
           key: 'salaryDate',
           type: FormTypes.date,
           width:"200px",
           placeholder: '请输入${title}',
           defaultValue:'',
         },
       ]
     },
     url: {
       add: "/authority/authorityRuleTest/add",
       edit: "/authority/authorityRuleTest/edit",
        authorityRuleTest: {
         list: '/authority/authorityRuleTest/queryById'
        },
       authorityRuleTestSub: {
         list: '/authority/authorityRuleTest/queryAuthorityRuleTestSubByMainId'
       },
     }
   }
 },
 methods: {
   getAllTable() {
     let values = this.tableKeys.map(key => getRefPromise(this, key))
     return Promise.all(values)
   },
   /** 调用完edit()方法之后会自动调用此方法 */
   editAfter() {
     this.$nextTick(() => {
     })
     // 加载子表数据
     if (this.model.id) {
       let params = { id: this.model.id }
       this.requestSubTableData(this.url.authorityRuleTestSub.list, params, this.authorityRuleTestSubTable)
     }
   },
   //校验所有一对一子表表单
   validateSubForm(allValues){
     return new Promise((resolve,reject)=>{
       Promise.all([
       ]).then(() => {
         resolve(allValues)
       }).catch(e => {
         reject(e)
       })
     })
   },
   /** 整理成formData */
   classifyIntoFormData(allValues) {
     let main = Object.assign(this.model, allValues.formValue)
     return {
       ...main, // 展开
       authorityRuleTestSubList: allValues.tablesValue[0].values,
     }
   },
    /** 确定按钮点击事件 */
     handleOk() {
       /** 触发表单验证 */
       this.getAllTable().then(tables => {
          return validateFormModelAndTables(this.$refs.form,this.model, tables)
       }).then(allValues => {
         /** 一次性验证一对一的所有子表 */
         return this.validateSubForm(allValues)
       }).then(allValues => {
         if (typeof this.classifyIntoFormData !== 'function') {
           throw this.throwNotFunction('classifyIntoFormData')
         }
         console.log("this.classifyIntoFormData",typeof this.classifyIntoFormData)
         let formData = this.classifyIntoFormData(allValues)

         // 发起请求
         return this.request(formData)
       }).catch(e => {
         if (e.error === VALIDATE_NO_PASSED) {
           // 如果有未通过表单验证的子表，就自动跳转到它所在的tab
           this.activeKey = e.index == null ? this.refKeys[0] : this.refKeys[e.index+1]
         } else {
           console.error(e)
         }
       })
     },
   validateError(msg){
     this.$message.error(msg)
   },
 close() {
   this.visible = false
   this.$emit('close')
   this.$refs.form.clearValidate();
 },

 }
}
</script>

<style scoped>
    /** tab panel 中有下拉框/日期 这类带下拉效果的，需要加此样式 */
    /deep/ .jeecg-tabs-top {
        overflow: visible;
    }
</style>