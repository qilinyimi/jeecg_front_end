<template>
  <j-form-container :disabled="disabled">
    <a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail" :label-col="labelCol">
      <a-row>一、自查自纠</a-row>
      <a-row class="Business">
        <div class="content-left">
          <span class="title">纳入自查范围企业级次分布:</span>
          <div class="center">
            <a-col>
              <a-form-model-item label="一级" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="entityOne">
                <a-input-number v-model="model.entityOne" placeholder="请输入按企业层级分布一级" style="width: 100%" />
              </a-form-model-item>
            </a-col>
            <a-col>
              <a-form-model-item label="二级" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="entityTwo">
                <a-input-number v-model="model.entityTwo" placeholder="请输入按企业层级分布二级" style="width: 100%" />
              </a-form-model-item>
            </a-col>
            <a-col>
              <a-form-model-item label="三级" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="entityThree">
                <a-input-number
                  v-model="model.entityThree"
                  placeholder="请输入按企业层级分布三级"
                  style="width: 100%"
                />
              </a-form-model-item>
            </a-col>
            <a-col>
              <a-form-model-item label="四级" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="entityFour">
                <a-input-number v-model="model.entityFour" placeholder="请输入按企业层级分布四级" style="width: 100%" />
              </a-form-model-item>
            </a-col>
            <a-col>
              <a-form-model-item label="五级及以下" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="entityFive">
                <a-input-number
                  v-model="model.entityFive"
                  placeholder="请输入按企业层级分布五级及以下"
                  style="width: 100%"
                />
              </a-form-model-item>
            </a-col>
            <a-col>
              <a-form-model-item label="合计" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="entityTotal">
                <a-input-number
                  v-model="model.entityTotal"
                  placeholder="请输入按企业层级分布合计"
                  style="width: 100%"
                />
              </a-form-model-item>
            </a-col>
          </div>
        </div>
        <div class="content-center">
          <span class="title">纳入自查范围项目类型分布</span>
          <div class="center">
            <a-row>
              <a-col :span="8">
                <a-form-model-item label="固定项目数量" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="imProNum">
                  <a-input-number v-model="model.imProNum" placeholder="请输入固定项目数量" style="width: 100%" />
                </a-form-model-item>
              </a-col>
              <a-col :span="8">
                <a-form-model-item label="固定计划投资" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="imProPlan">
                  <a-input-number v-model="model.imProPlan" placeholder="请输入固定计划投资" style="width: 100%" />
                </a-form-model-item>
              </a-col>
              <a-col :span="8">
                <a-form-model-item label="固定完成投资" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="imProFns">
                  <a-input-number v-model="model.imProFns" placeholder="请输入固定完成投资" style="width: 100%" />
                </a-form-model-item>
              </a-col>
              <a-col :span="8">
                <a-form-model-item label="股权项目数量" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="eqProNum">
                  <a-input-number v-model="model.eqProNum" placeholder="请输入股权项目数量" style="width: 100%" />
                </a-form-model-item>
              </a-col>
              <a-col :span="8">
                <a-form-model-item label="股权计划投资" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="eqProPlan">
                  <a-input-number v-model="model.eqProPlan" placeholder="请输入股权计划投资" style="width: 100%" />
                </a-form-model-item>
              </a-col>
              <a-col :span="8">
                <a-form-model-item label="股权完成投资" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="eqProFns">
                  <a-input-number v-model="model.eqProFns" placeholder="请输入股权完成投资" style="width: 100%" />
                </a-form-model-item>
              </a-col>
              <a-col :span="8">
                <a-form-model-item label="合计项目数量" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="typeProNum">
                  <a-input-number v-model="model.typeProNum" placeholder="请输入合计项目数量" style="width: 100%" />
                </a-form-model-item>
              </a-col>
              <a-col :span="8">
                <a-form-model-item
                  label="合计计划投资"
                  :labelCol="labelCol"
                  :wrapperCol="wrapperCol"
                  prop="typeProPlan"
                >
                  <a-input-number v-model="model.typeProPlan" placeholder="请输入合计计划投资" style="width: 100%" />
                </a-form-model-item>
              </a-col>
              <a-col :span="8">
                <a-form-model-item label="合计完成投资" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="typeProFns">
                  <a-input-number v-model="model.typeProFns" placeholder="请输入合计完成投资" style="width: 100%" />
                </a-form-model-item>
              </a-col>
            </a-row>
          </div>
        </div>
        <div class="content-right">
          <span class="title">审计、巡视及专项监督检查发现的投资方面问题分布</span>
          <div class="center">
            <a-col>
              <a-form-model-item label="审计" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="inspNum">
                <a-input-number v-model="model.inspNum" placeholder="请输入审计" style="width: 100%" />
              </a-form-model-item>
            </a-col>
            <a-col>
              <a-form-model-item label="巡视" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="patrolNum">
                <a-input-number v-model="model.patrolNum" placeholder="请输入巡视" style="width: 100%" />
              </a-form-model-item>
            </a-col>
            <a-col>
              <a-form-model-item label="专项" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="specNum">
                <a-input-number v-model="model.specNum" placeholder="请输入专项" style="width: 100%" />
              </a-form-model-item>
            </a-col>
            <a-col>
              <a-form-model-item label="其他" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="otherNum">
                <a-input-number v-model="model.otherNum" placeholder="请输入其他" style="width: 100%" />
              </a-form-model-item>
            </a-col>
            <a-col>
              <a-form-model-item label="合计" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="totNum">
                <a-input-number v-model="model.totNum" placeholder="请输入合计" style="width: 100%" />
              </a-form-model-item>
            </a-col>
            <a-col>
              <a-form-model-item label="外键" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="inspId">
                <a-input v-model="model.inspId" placeholder="请输入外键"></a-input>
              </a-form-model-item>
            </a-col>
          </div>
        </div>
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
  name: 'SelInsp1Form',
  components: {
    JFormContainer,
  },
  props: {
    disabled: {
      type: Boolean,
      default: false,
      required: false,
    },
  },
  data() {
    return {
      model: {},
      labelCol: {
        xs: { span: 24 },
        sm: { span: 8 },
      },
      wrapperCol: {
        xs: { span: 24 },
        sm: { span: 16 },
      },
      validatorRules: {
        entityOne: [{ required: true, message: '请输入按企业层级分布一级!' }],
        entityTwo: [{ required: true, message: '请输入按企业层级分布二级!' }],
        entityThree: [{ required: true, message: '请输入按企业层级分布三级!' }],
        entityFour: [{ required: true, message: '请输入按企业层级分布四级!' }],
        entityFive: [{ required: true, message: '请输入按企业层级分布五级及以下!' }],
        entityTotal: [{ required: true, message: '请输入按企业层级分布合计!' }],
        imProNum: [{ required: true, message: '请输入固定项目数量!' }],
        imProPlan: [{ required: true, message: '请输入固定计划投资!' }],
        imProFns: [{ required: true, message: '请输入固定完成投资!' }],
        eqProNum: [{ required: true, message: '请输入股权项目数量!' }],
        eqProPlan: [{ required: true, message: '请输入股权计划投资!' }],
        eqProFns: [{ required: true, message: '请输入股权完成投资!' }],
        typeProNum: [{ required: true, message: '请输入合计项目数量!' }],
        typeProPlan: [{ required: true, message: '请输入合计计划投资!' }],
        typeProFns: [{ required: true, message: '请输入合计完成投资!' }],
        inspNum: [{ required: true, message: '请输入审计!' }],
        patrolNum: [{ required: true, message: '请输入巡视!' }],
        specNum: [{ required: true, message: '请输入专项!' }],
        otherNum: [{ required: true, message: '请输入其他!' }],
        totNum: [{ required: true, message: '请输入合计!' }],
      },
      confirmLoading: false,
    }
  },
  created() {
    //备份model原始值
    this.modelDefault = JSON.parse(JSON.stringify(this.model))
  },
  methods: {
    initFormData(url, id) {
      this.clearFormData()
      if (!id) {
        this.edit(this.modelDefault)
      } else {
        getAction(url, { id: id }).then((res) => {
          if (res.success) {
            let records = res.result
            if (records && records.length > 0) {
              this.edit(records[0])
            }
          }
        })
      }
    },
    edit(record) {
      this.model = Object.assign({}, record)
    },
    getFormData() {
      let formdata_arr = []
      this.$refs.form.validate((valid) => {
        if (valid) {
          let isNullObj = true
          Object.keys(this.model).forEach((key) => {
            if (this.model[key]) {
              isNullObj = false
            }
          })
          if (!isNullObj) {
            formdata_arr.push(this.model)
          }
        } else {
          this.$emit('validateError', '总体情况表单校验未通过')
        }
      })
      return formdata_arr
    },
    validate(index) {
      return new Promise((resolve, reject) => {
        // 验证主表表单
        this.$refs.form.validate((valid) => {
          !valid ? reject({ error: VALIDATE_FAILED, index }) : resolve()
        })
      })
        .then((values) => {
          return Promise.resolve()
        })
        .catch((error) => {
          return Promise.reject(error)
        })
    },
    clearFormData() {
      this.$refs.form.clearValidate()
    },
  },
}
</script>
<style lang="less" scoped>
.Business {
  display: flex;
  border: 1px solid #666;
  min-height: 100px;
  .title {
    width: 80px;
    padding: 0 5px;
    border-right: 1px solid;
  }
  .content-left {
    display: flex;
    // flex-direction: column;
    .center {
      flex: 1;
      .ant-form-item {
        margin: 0;
        border: 1px solid #ddd;
      }
    }
  }
  .content-center {
    max-width: 50%;
    border-left: 1px solid #333;
    border-right: 1px solid #333;
    display: flex;
    .center {
      flex: 1;
      .ant-form-item {
        margin: 0;
        border: 1px solid #ddd;
      }
    }
  }
  .content-right {
    display: flex;
    .center {
      flex: 1;
      .ant-form-item {
        margin: 0;
        border: 1px solid #ddd;
      }
    }
  }
}
</style>