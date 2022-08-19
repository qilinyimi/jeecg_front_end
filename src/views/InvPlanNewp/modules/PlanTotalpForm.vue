<template>
  <j-form-container :disabled="disabled">
      <a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
        <a-row>
          <a-col :span="12">
            <a-form-model-item label="固定资产总额" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="imInvTotal">
              <a-input-number v-model="model.imInvTotal" placeholder="请输入固定资产总额" style="width: 100%"/>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="境内固定总额" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="imInvTotalIn">
              <a-input-number v-model="model.imInvTotalIn" placeholder="请输入境内固定总额" style="width: 100%"/>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="境外固定总额" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="imInvTotalOut">
              <a-input-number v-model="model.imInvTotalOut" placeholder="请输入境外固定总额" style="width: 100%"/>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="新开工境内固定" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="imInvTotalInNew">
              <a-input-number v-model="model.imInvTotalInNew" placeholder="请输入新开工境内固定" style="width: 100%"/>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="续建境内固定" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="imInvTotalOutMer">
              <a-input-number v-model="model.imInvTotalOutMer" placeholder="请输入续建境内固定" style="width: 100%"/>
            </a-form-model-item>
          </a-col>
        </a-row>
      </a-form-model>
  </j-form-container>
</template>
<script>
  import { getAction } from '@/api/manage'
  import { validateDuplicateValue } from '@/utils/util'
  import JFormContainer from '@/components/jeecg/JFormContainer'
  import { VALIDATE_NO_PASSED } from '@/utils/JEditableTableUtil'

  export default {
    name: 'PlanTotalpForm',
    components: {
      JFormContainer
    },
    props:{
      disabled: {
        type: Boolean,
        default: false,
        required: false
      }
    },
    data () {
      return {
        model: {
        },
        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },
        validatorRules: {
        },
        confirmLoading: false,
      }
    },
    created () {
     //备份model原始值
      this.modelDefault = JSON.parse(JSON.stringify(this.model));
    },
    methods:{
      initFormData(url,id){
        this.clearFormData()
        if(!id){
         this.edit(this.modelDefault);
        }else{
          getAction(url,{id:id}).then(res=>{
            if(res.success){
              let records = res.result
              if(records && records.length>0){
                this.edit(records[0])
              }
            }
          })
        }
      },
      edit(record){
        this.model = Object.assign({}, record)
        console.log("PlanTotalpForm-edit",this.model);
      },
      getFormData(){
        let formdata_arr = []
        this.$refs.form.validate(valid => {
          if (valid) {
            let isNullObj = true
            Object.keys(this.model).forEach(key=>{
              if( this.model[key]){
                isNullObj = false
              }
            })
            if(!isNullObj){
              formdata_arr.push(this.model)
            }
          }else{
            this.$emit("validateError","计划投资额表表单校验未通过");
            return false
          }
        })
        return formdata_arr;
      },
      validate(index){
        return new Promise((resolve, reject) => {
          // 验证主表表单
         this.$refs.form.validate(valid => {
            !valid ? reject({ error: VALIDATE_NO_PASSED ,index}) : resolve()
          })
        }).then(values => {
          return Promise.resolve()
        }).catch(error => {
          return Promise.reject(error)
        })
      },
      clearFormData(){
        this.$refs.form.clearValidate()
      },
    }
  }
</script>
