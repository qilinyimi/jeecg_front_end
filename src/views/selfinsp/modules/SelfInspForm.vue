<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container :disabled="formDisabled">
      <!-- 主表单区域 -->
      <a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
        <a-row>
          <a-col :span="24" >
            <a-form-model-item label="公司名称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="comp">
              <a-input v-model="model.comp" placeholder="请输入公司名称" ></a-input>
            </a-form-model-item>
          </a-col>
        </a-row>
      </a-form-model>
    </j-form-container>
      <!-- 子表单区域 -->
    <a-tabs v-model="activeKey" @change="handleChangeTabs">
      <a-tab-pane tab="总体情况" :key="refKeys[0]" :forceRender="true">
        <sel-insp1-form ref="selInsp1Form" @validateError="validateError" :disabled="formDisabled"></sel-insp1-form>
      </a-tab-pane>
      
      <a-tab-pane tab="基本信息表" :key="refKeys[1]" :forceRender="true">
        <sel-insp2-form ref="selInsp2Form" @validateError="validateError" :disabled="formDisabled"></sel-insp2-form>
      </a-tab-pane>
      
      <a-tab-pane tab="发现问题明细表" :key="refKeys[2]" :forceRender="true">
        <j-vxe-table
          keep-source
          :ref="refKeys[2]"
          :loading="selInsp21Table.loading"
          :columns="selInsp21Table.columns"
          :dataSource="selInsp21Table.dataSource"
          :maxHeight="300"
          :disabled="formDisabled"
          :rowNumber="true"
          :rowSelection="true"
          :toolbar="true"
          />
      </a-tab-pane>
      <a-tab-pane tab="项目明细表" :key="refKeys[3]" :forceRender="true">
        <j-vxe-table
          keep-source
          :ref="refKeys[3]"
          :loading="selInsp22Table.loading"
          :columns="selInsp22Table.columns"
          :dataSource="selInsp22Table.dataSource"
          :maxHeight="300"
          :disabled="formDisabled"
          :rowNumber="true"
          :rowSelection="true"
          :toolbar="true"
          />
      </a-tab-pane>
      <a-tab-pane tab="分布表" :key="refKeys[4]" :forceRender="true">
        <j-vxe-table
          keep-source
          :ref="refKeys[4]"
          :loading="selInsp221Table.loading"
          :columns="selInsp221Table.columns"
          :dataSource="selInsp221Table.dataSource"
          :maxHeight="300"
          :disabled="formDisabled"
          :rowNumber="true"
          :rowSelection="true"
          :toolbar="true"
          />
      </a-tab-pane>
      <a-tab-pane tab="历史问题表" :key="refKeys[5]" :forceRender="true">
        <j-vxe-table
          keep-source
          :ref="refKeys[5]"
          :loading="selInsp23Table.loading"
          :columns="selInsp23Table.columns"
          :dataSource="selInsp23Table.dataSource"
          :maxHeight="300"
          :disabled="formDisabled"
          :rowNumber="true"
          :rowSelection="true"
          :toolbar="true"
          />
      </a-tab-pane>
      <a-tab-pane tab="总体情况" :key="refKeys[6]" :forceRender="true">
        <j-vxe-table
          keep-source
          :ref="refKeys[6]"
          :loading="selInsp3Table.loading"
          :columns="selInsp3Table.columns"
          :dataSource="selInsp3Table.dataSource"
          :maxHeight="300"
          :disabled="formDisabled"
          :rowNumber="true"
          :rowSelection="true"
          :toolbar="true"
          />
      </a-tab-pane>
    </a-tabs>
    <a-row v-if="showFlowSubmitButton" style="text-align: center;width: 100%;margin-top: 16px;"><a-button icon="check" style="width: 126px" type="primary" @click="handleOk">提 交</a-button></a-row>
  </a-spin>
</template>

<script>

  import { getAction } from '@/api/manage'
  import { JVxeTableModelMixin } from '@/mixins/JVxeTableModelMixin.js'
  import { JVXETypes } from '@/components/jeecg/JVxeTable'
  import { getRefPromise,VALIDATE_FAILED} from '@/components/jeecg/JVxeTable/utils/vxeUtils.js'
  import { validateDuplicateValue } from '@/utils/util'
  import JFormContainer from '@/components/jeecg/JFormContainer'
  import SelInsp1Form from './SelInsp1Form.vue'
  import SelInsp2Form from './SelInsp2Form.vue'

  export default {
    name: 'SelfInspForm',
    mixins: [JVxeTableModelMixin],
    components: {
      JFormContainer,
      SelInsp1Form,
      SelInsp2Form,
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
        model:{
         },
        // 新增时子表默认添加几行空数据
        addDefaultRowNum: 1,
        validatorRules: {
        },
        refKeys: ['selInsp1', 'selInsp2', 'selInsp21', 'selInsp22', 'selInsp221', 'selInsp23', 'selInsp3', ],
        tableKeys:['selInsp21', 'selInsp22', 'selInsp221', 'selInsp23', 'selInsp3', ],
        activeKey: 'selInsp1',
        // 总体情况
        selInsp1Table: {
          loading: false,
          dataSource: [],
          columns: [
          ]
        },
        // 基本信息表
        selInsp2Table: {
          loading: false,
          dataSource: [],
          columns: [
          ]
        },
        // 发现问题明细表
        selInsp21Table: {
          loading: false,
          dataSource: [],
          columns: [
            {
              title: '涉及企业名称',
              key: 'enterName',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '管理层级',
              key: 'manageRank',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '发现问题',
              key: 'findProblem',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '问题类型',
              key: 'problemType',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '具体表现',
              key: 'specPerf',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '整改举措',
              key: 'rectDesc',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '整改举措数量',
              key: 'rectNum',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '整改预计完成时间',
              key: 'rectTime',
              type: JVXETypes.date,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
          ]
        },
        // 项目明细表
        selInsp22Table: {
          loading: false,
          dataSource: [],
          columns: [
            {
              title: '项目名称',
              key: 'proName',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '项目存在问题描述',
              key: 'problemDesc',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '涉及的问题数量',
              key: 'problemNum',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '整改举措',
              key: 'rectDesc',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '整改举措数量',
              key: 'rectNum',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '整改预计完成时间',
              key: 'rectTime',
              type: JVXETypes.date,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '项目计划总投资',
              key: 'proPlanTot',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '项目实际完成投资额',
              key: 'proFnsTot',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '项目类型',
              key: 'proType',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '是否主业',
              key: 'hasMain',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '境内或境外',
              key: 'hasIn',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '项目地点',
              key: 'proLocation',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '所属行业',
              key: 'proIndusty',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '项目阶段',
              key: 'proPhuse',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '问题涉及企业名称',
              key: 'proEnterName',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '问题涉及企业管理层级',
              key: 'proEnterRank',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
          ]
        },
        // 分布表
        selInsp221Table: {
          loading: false,
          dataSource: [],
          columns: [
            {
              title: '类型的code',
              key: 'code',
              type: JVXETypes.popup,
              popupCode:"self_insp_pro",
              field:"code,name",
              orgFields:"code,name",
              destFields:"code,name",
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '类型的名称',
              key: 'name',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '存在问题的项目名称',
              key: 'proName',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '存在问题的项目数量',
              key: 'proNum',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '计划投资额',
              key: 'proPlan',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '实际完成额',
              key: 'proFns',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },


          ]
        },
        // 历史问题表
        selInsp23Table: {
          loading: false,
          dataSource: [],
          columns: [
            {
              title: '问题描述',
              key: 'proDesc',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '是否完成整改',
              key: 'type',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '审计名称',
              key: 'protalName',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '审计开展年度',
              key: 'protalYear',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '未完成原因',
              key: 'imcompReson',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '项目数量',
              key: 'proNum',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '项目计划总投资',
              key: 'proPlan',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '项目实际完成额',
              key: 'proFns',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '整改举措',
              key: 'rectDesc',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '整改举措数量',
              key: 'rectNum',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '整改完成时间',
              key: 'rectTime',
              type: JVXETypes.date,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
          ]
        },
        // 总体情况
        selInsp3Table: {
          loading: false,
          dataSource: [],
          columns: [
            {
              title: '企业名称',
              key: 'enterName',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '管理层级',
              key: 'manageRank',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '整改举措',
              key: 'rectDesc',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '整改数量',
              key: 'rectNum',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '整改预计完成时间',
              key: 'rectTime',
              type: JVXETypes.date,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '新建制度',
              key: 'newSys',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '修订完善制度',
              key: 'editSys',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '清理退出项目数量',
              key: 'cleanProNum',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '清理退出项目总投资',
              key: 'cleanProPlan',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '挽回资产损失金额',
              key: 'toSave',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '其他方面成效',
              key: 'otherResult',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
          ]
        },
        url: {
          add: "/selfinsp/selfInsp/add",
          edit: "/selfinsp/selfInsp/edit",
          queryById: "/selfinsp/selfInsp/queryById",
          selInsp1: {
            list: '/selfinsp/selfInsp/querySelInsp1ByMainId'
          },
          selInsp2: {
            list: '/selfinsp/selfInsp/querySelInsp2ByMainId'
          },
          selInsp21: {
            list: '/selfinsp/selfInsp/querySelInsp21ByMainId'
          },
          selInsp22: {
            list: '/selfinsp/selfInsp/querySelInsp22ByMainId'
          },
          selInsp221: {
            list: '/selfinsp/selfInsp/querySelInsp221ByMainId'
          },
          selInsp23: {
            list: '/selfinsp/selfInsp/querySelInsp23ByMainId'
          },
          selInsp3: {
            list: '/selfinsp/selfInsp/querySelInsp3ByMainId'
          },
        }
      }
    },
    props: {
      //流程表单data
      formData: {
        type: Object,
        default: ()=>{},
        required: false
      },
      //表单模式：false流程表单 true普通表单
      formBpm: {
        type: Boolean,
        default: false,
        required: false
      },
      //表单禁用
      disabled: {
        type: Boolean,
        default: false,
        required: false
      }
    },
    computed: {
      formDisabled(){
        if(this.formBpm===true){
          if(this.formData.disabled===false){
            return false
          }
          return true
        }
        return this.disabled
      },
      showFlowSubmitButton(){
        if(this.formBpm===true){
          if(this.formData.disabled===false){
            return true
          }
        }
        return false
      }
    },
    created () {
      //如果是流程中表单，则需要加载流程表单data
      this.showFlowData();
    },
    methods: {
      addBefore(){
        this.$refs.selInsp1Form.clearFormData()
        this.$refs.selInsp2Form.clearFormData()
        this.selInsp21Table.dataSource=[]
        this.selInsp22Table.dataSource=[]
        this.selInsp221Table.dataSource=[]
        this.selInsp23Table.dataSource=[]
        this.selInsp3Table.dataSource=[]
      },
      getAllTable() {
        let values = this.tableKeys.map(key => getRefPromise(this, key))
        return Promise.all(values)
      },
      /** 调用完edit()方法之后会自动调用此方法 */
      editAfter() {
        this.$nextTick(() => {
          this.$refs.selInsp1Form.initFormData(this.url.selInsp1.list,this.model.id)
          this.$refs.selInsp2Form.initFormData(this.url.selInsp2.list,this.model.id)
        })
        // 加载子表数据
        if (this.model.id) {
          let params = { id: this.model.id }
          this.requestSubTableData(this.url.selInsp21.list, params, this.selInsp21Table)
          this.requestSubTableData(this.url.selInsp22.list, params, this.selInsp22Table)
          this.requestSubTableData(this.url.selInsp221.list, params, this.selInsp221Table)
          this.requestSubTableData(this.url.selInsp23.list, params, this.selInsp23Table)
          this.requestSubTableData(this.url.selInsp3.list, params, this.selInsp3Table)
        }
      },
      //校验所有一对一子表表单
        validateSubForm(allValues){
            return new Promise((resolve,reject)=>{
              Promise.all([
                  this.$refs.selInsp1Form.validate(0),
                  this.$refs.selInsp2Form.validate(1),
              ]).then(() => {
                resolve(allValues)
              }).catch(e => {
                if (e.error === VALIDATE_FAILED) {
                  // 如果有未通过表单验证的子表，就自动跳转到它所在的tab
                  this.activeKey = e.index == null ? this.activeKey : this.refKeys[e.index]
                } else {
                  console.error(e)
                }
              })
            })
        },
      /** 整理成formData */
      classifyIntoFormData(allValues) {
        let main = Object.assign(this.model, allValues.formValue)
        return {
          ...main, // 展开
          selInsp1List: this.$refs.selInsp1Form.getFormData(),
          selInsp2List: this.$refs.selInsp2Form.getFormData(),
          selInsp21List: allValues.tablesValue[0].tableData,
          selInsp22List: allValues.tablesValue[1].tableData,
          selInsp221List: allValues.tablesValue[2].tableData,
          selInsp23List: allValues.tablesValue[3].tableData,
          selInsp3List: allValues.tablesValue[4].tableData,
        }
      },
      //渲染流程表单数据
      showFlowData(){
        if(this.formBpm === true){
          let params = {id:this.formData.dataId};
          getAction(this.url.queryById,params).then((res)=>{
            if(res.success){
              this.edit (res.result);
            }
          })
        }
      },
      validateError(msg){
        this.$message.error(msg)
      },

    }
  }
</script>

<style scoped>
</style>