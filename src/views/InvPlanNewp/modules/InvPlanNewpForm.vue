<template>
<a-spin :spinning="confirmLoading">

   <a-tabs v-model="activeKey" @change="handleChangeTabs">
   <!--主表区域 -->
    <a-tab-pane tab="投资计划新" :key="refKeys[0]" :forceRender="true" :class="'jeecg-tabs-top'" :animated="false">
         <a-form-model ref="form" :model="model" :rules="validatorRules">
           <a-row>
                  <a-col :xs="24" :sm="12">
                    <a-form-model-item label="创建人" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="createBy">
                      <a-input v-model="model.createBy" placeholder="请输入创建人" disabled></a-input>
                    </a-form-model-item>
                  </a-col>
                  <a-col :xs="24" :sm="12">
                    <a-form-model-item label="投资计划年" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="invyear">
                      <j-date placeholder="请选择投资计划年" v-model="model.invyear" style="width: 100%" />
                    </a-form-model-item>
                  </a-col>
                  <a-col :xs="24" :sm="12">
                    <a-form-model-item label="项目状态" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="state">
                      <a-input v-model="model.state" placeholder="请输入项目状态" ></a-input>
                    </a-form-model-item>
                  </a-col>

                </a-row>
              </a-form-model>

     </a-tab-pane>
   <!--子表单区域 -->
     <a-tab-pane tab="投资计划项目新" :key="refKeys[1]" :forceRender="true">
       <j-editable-table
         :ref="refKeys[1]"
         :loading="invPlanProNewpTable.loading"
         :columns="invPlanProNewpTable.columns"
         :dataSource="invPlanProNewpTable.dataSource"
         :maxHeight="300"
         :rowNumber="true"
         :rowSelection="true"
         :actionButton="true"/>
     </a-tab-pane>

     <a-tab-pane tab="计划投资额表" :key="refKeys[2]" :forceRender="true">
       <plan-totalp-form ref="planTotalpForm" @validateError="validateError"></plan-totalp-form>
     </a-tab-pane>

   </a-tabs>

 </a-spin>
</template>

<script>

import { FormTypes,getRefPromise } from '@/utils/JEditableTableUtil'
import { JEditableTableModelMixin } from '@/mixins/JEditableTableModelMixin'
import { validateDuplicateValue } from '@/utils/util'
import { VALIDATE_NO_PASSED, validateFormModelAndTables } from '@/utils/JEditableTableUtil'
import PlanTotalpForm from './PlanTotalpForm.vue'

export default {
 name: 'InvPlanNewpForml',
 mixins: [JEditableTableModelMixin],
 components: {
 PlanTotalpForm,
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
     refKeys: ['invPlanNewp','invPlanProNewp', 'planTotalp', ],
     tableKeys:['invPlanProNewp', ],
     activeKey: 'invPlanNewp',
     // 投资计划项目新
     invPlanProNewpTable: {
       loading: false,
       dataSource: [],
       columns: [
         {
           title: '项目的id',
           key: 'projectid',
           type: FormTypes.select,
           dictCode:"project,proname,id",
           width:"200px",
           placeholder: '请输入${title}',
           defaultValue:'',
         },
       ]
     },
     // 计划投资额表
     planTotalpTable: {
       loading: false,
       dataSource: [],
       columns: [
       ]
     },
     url: {
       add: "/InvPlanNewp/invPlanNewp/add",
       edit: "/InvPlanNewp/invPlanNewp/edit",
        invPlanNewp: {
         list: '/InvPlanNewp/invPlanNewp/queryById'
        },
       invPlanProNewp: {
         list: '/InvPlanNewp/invPlanNewp/queryInvPlanProNewpByMainId'
       },
       planTotalp: {
         list: '/InvPlanNewp/invPlanNewp/queryPlanTotalpByMainId'
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
       this.$refs.planTotalpForm.initFormData(this.url.planTotalp.list,this.model.id)
     })
     // 加载子表数据
     if (this.model.id) {
       let params = { id: this.model.id }
       this.requestSubTableData(this.url.invPlanProNewp.list, params, this.invPlanProNewpTable)
     }
   },
   //校验所有一对一子表表单
   validateSubForm(allValues){
     return new Promise((resolve,reject)=>{
       Promise.all([
           this.$refs.planTotalpForm.validate(1),
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
       invPlanProNewpList: allValues.tablesValue[0].values,
       planTotalpList: this.$refs.planTotalpForm.getFormData(),
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