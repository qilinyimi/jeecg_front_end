<template>
  <j-form-container :disabled="disabled">
      <a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
        <a-row>
          <a-col :span="24">
            <a-form-model-item label="部门名称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="baseOrgName">
              <a-input v-model="model.baseOrgName" placeholder="请输入部门名称"></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="主要职责" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="baseMainWork">
              <a-input v-model="model.baseMainWork" placeholder="请输入主要职责"></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="文件总数量" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="baseManaFileNum">
              <a-input-number v-model="model.baseManaFileNum" placeholder="请输入文件总数量" style="width: 100%"/>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="系统建立时间" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="baseSysTime">
              <j-date placeholder="请选择系统建立时间" v-model="model.baseSysTime"  style="width: 100%"/>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="系统建立名称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="baseSysName">
              <a-input v-model="model.baseSysName" placeholder="请输入系统建立名称"></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="一级制度数量" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="baseRankOne">
              <a-input-number v-model="model.baseRankOne" placeholder="请输入一级制度数量" style="width: 100%"/>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="二级制度数量" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="baseRankTwo">
              <a-input-number v-model="model.baseRankTwo" placeholder="请输入二级制度数量" style="width: 100%"/>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="三级制度数量" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="baseRankThree">
              <a-input-number v-model="model.baseRankThree" placeholder="请输入三级制度数量" style="width: 100%"/>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="四级制度数量" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="baseRankFour">
              <a-input-number v-model="model.baseRankFour" placeholder="请输入四级制度数量" style="width: 100%"/>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="五级及以下制度数量" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="baseRankFive">
              <a-input-number v-model="model.baseRankFive" placeholder="请输入五级及以下制度数量" style="width: 100%"/>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="合计制度数量" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="baseRankTot">
              <a-input-number v-model="model.baseRankTot" placeholder="请输入合计制度数量" style="width: 100%"/>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="不完善一级企业" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="noimpRankOne">
              <a-input-number v-model="model.noimpRankOne" placeholder="请输入不完善一级企业" style="width: 100%"/>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="不完善二级企业数量" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="noimpRankTwo">
              <a-input-number v-model="model.noimpRankTwo" placeholder="请输入不完善二级企业数量" style="width: 100%"/>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="不完善三级企业数量" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="noimpRankThree">
              <a-input-number v-model="model.noimpRankThree" placeholder="请输入不完善三级企业数量" style="width: 100%"/>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="不完善四级企业数量" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="noimpRankFour">
              <a-input-number v-model="model.noimpRankFour" placeholder="请输入不完善四级企业数量" style="width: 100%"/>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="不完成五级及以下企业数量" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="noimpRankFive">
              <a-input-number v-model="model.noimpRankFive" placeholder="请输入不完成五级及以下企业数量" style="width: 100%"/>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="不完成合计企业数量" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="noimpRankTot">
              <a-input-number v-model="model.noimpRankTot" placeholder="请输入不完成合计企业数量" style="width: 100%"/>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="外键" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="inspId">
              <a-input v-model="model.inspId" placeholder="请输入外键"></a-input>
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
 import { VALIDATE_FAILED } from '@/components/jeecg/JVxeTable/utils/vxeUtils.js'
  export default {
    name: 'SelInsp2Form',
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
        model:{
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
           baseRankOne: [
              { required: true, message: '请输入一级制度数量!'},
           ],
           baseRankTwo: [
              { required: true, message: '请输入二级制度数量!'},
           ],
           baseRankThree: [
              { required: true, message: '请输入三级制度数量!'},
           ],
           baseRankFour: [
              { required: true, message: '请输入四级制度数量!'},
           ],
           baseRankFive: [
              { required: true, message: '请输入五级及以下制度数量!'},
           ],
           baseRankTot: [
              { required: true, message: '请输入合计制度数量!'},
           ],
           noimpRankOne: [
              { required: true, message: '请输入不完善一级企业!'},
           ],
           noimpRankTwo: [
              { required: true, message: '请输入不完善二级企业数量!'},
           ],
           noimpRankThree: [
              { required: true, message: '请输入不完善三级企业数量!'},
           ],
           noimpRankFour: [
              { required: true, message: '请输入不完善四级企业数量!'},
           ],
           noimpRankFive: [
              { required: true, message: '请输入不完成五级及以下企业数量!'},
           ],
           noimpRankTot: [
              { required: true, message: '请输入不完成合计企业数量!'},
           ],
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
          this.edit(this.modelDefault)
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
      },
      getFormData(){
        let formdata_arr = []
        this.$refs.form.validate(valid => {
          if (valid) {
            let isNullObj = true
            Object.keys(this.model).forEach(key=>{
              if(this.model[key]){
                isNullObj = false
              }
            })
            if(!isNullObj){
              formdata_arr.push(this.model)
            }
          }else{
            this.$emit("validateError","基本信息表表单校验未通过");
          }
        })
        return formdata_arr;
      },
       validate(index){
        return new Promise((resolve, reject) => {
          // 验证主表表单
         this.$refs.form.validate(valid => {
            !valid ? reject({ error: VALIDATE_FAILED ,index}) : resolve()
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
