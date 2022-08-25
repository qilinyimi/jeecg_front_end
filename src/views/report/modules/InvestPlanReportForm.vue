<template>
  <a-spin :spinning="confirmLoading">
    <a-tabs v-model="activeKey" @change="handleChangeTabs">
      <!--主表区域 -->
      <a-tab-pane tab="投资计划上报" :key="refKeys[0]" :forceRender="true" :class="'jeecg-tabs-top'" :animated="false">
        <a-form-model ref="form" :model="model" :rules="validatorRules">
          <a-row>
            <a-col :xs="24" :sm="12">
              <a-form-model-item label="状态名称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="stateName">
                <a-input v-model="model.stateName" placeholder="请输入状态名称"></a-input>
              </a-form-model-item>
            </a-col>
            <a-col :xs="24" :sm="12">
              <a-form-model-item label="状态编码" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="stateCode">
                <a-input v-model="model.stateCode" placeholder="请输入状态编码"></a-input>
              </a-form-model-item>
            </a-col>
            <a-col :xs="24" :sm="12">
              <a-form-model-item label="编号" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="code">
                <a-input v-model="model.code" placeholder="请输入编号"></a-input>
              </a-form-model-item>
            </a-col>
            <a-col :xs="24" :sm="12">
              <a-form-model-item label="上报单位" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="reportUnitName">
                <a-input v-model="model.reportUnitName" placeholder="请输入上报单位"></a-input>
              </a-form-model-item>
            </a-col>
            <a-col :xs="24" :sm="12">
              <a-form-model-item label="投资年度" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="year">
                <j-year-picker placeholder="选择年份" v-model="model.year" style="width: 100%" />
              </a-form-model-item>
            </a-col>
            <a-col :xs="24" :sm="12">
              <a-form-model-item label="版本号" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="versionNo">
                <a-input v-model="model.versionNo" placeholder="请输入版本号"></a-input>
              </a-form-model-item>
            </a-col>
            <a-col :xs="24" :sm="12">
              <a-form-model-item label="项目数" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="projectCount">
                <a-input-number v-model="model.projectCount" placeholder="请输入项目数" style="width: 100%" />
              </a-form-model-item>
            </a-col>
            <a-col :xs="24" :sm="12">
              <a-form-model-item label="提交人" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="submitterId">
                <a-input v-model="model.submitterId" placeholder="请输入提交人"></a-input>
              </a-form-model-item>
            </a-col>
            <a-col :xs="24" :sm="12">
              <a-form-model-item label="提交人Name" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="submitterName">
                <a-input v-model="model.submitterName" placeholder="请输入提交人Name"></a-input>
              </a-form-model-item>
            </a-col>
            <a-col :xs="24" :sm="12">
              <a-form-model-item label="提交时间" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="submitTime">
                <a-input v-model="model.submitTime" placeholder="请输入提交时间"></a-input>
              </a-form-model-item>
            </a-col>
            <a-col :xs="24" :sm="12">
              <a-form-model-item
                label="本级投资总资金"
                :labelCol="labelCol"
                :wrapperCol="wrapperCol"
                prop="currentAmount"
              >
                <a-input-number v-model="model.currentAmount" placeholder="请输入本级投资总资金" style="width: 100%" />
              </a-form-model-item>
            </a-col>
            <a-col :xs="24" :sm="12">
              <a-form-model-item
                label="下级投资总资金"
                :labelCol="labelCol"
                :wrapperCol="wrapperCol"
                prop="lowerAmount"
              >
                <a-input-number v-model="model.lowerAmount" placeholder="请输入下级投资总资金" style="width: 100%" />
              </a-form-model-item>
            </a-col>
            <a-col :xs="24" :sm="12">
              <a-form-model-item label="投资总资金" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="totalAmount">
                <a-input-number v-model="model.totalAmount" placeholder="请输入投资总资金" style="width: 100%" />
              </a-form-model-item>
            </a-col>
            <a-col :xs="24" :sm="12">
              <a-form-model-item label="登记人ID" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="creatorId">
                <a-input v-model="model.creatorId" placeholder="请输入登记人ID"></a-input>
              </a-form-model-item>
            </a-col>
            <a-col :xs="24" :sm="12">
              <a-form-model-item label="登记人Name" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="creatorName">
                <a-input v-model="model.creatorName" placeholder="请输入登记人Name"></a-input>
              </a-form-model-item>
            </a-col>
            <a-col :xs="24" :sm="12">
              <a-form-model-item label="修改人ID" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="modifierId">
                <a-input v-model="model.modifierId" placeholder="请输入修改人ID"></a-input>
              </a-form-model-item>
            </a-col>
            <a-col :xs="24" :sm="12">
              <a-form-model-item label="修改人Name" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="modifierName">
                <a-input v-model="model.modifierName" placeholder="请输入修改人Name"></a-input>
              </a-form-model-item>
            </a-col>
          </a-row>
        </a-form-model>
      </a-tab-pane>
      <!--子表单区域 -->
      <a-tab-pane tab="投资计划关联年度投资计划" :key="refKeys[1]" :forceRender="true">
        <a-button type="primary" @click="handleRelated">从项目库导入</a-button>
        <a-button type="primary" @click="handleNotice">一键通知</a-button>
        <j-editable-table
          :ref="refKeys[1]"
          :loading="investPlanRelationDetailTable.loading"
          :columns="investPlanRelationDetailTable.columns"
          :dataSource="investPlanRelationDetailTable.dataSource"
          :maxHeight="300"
          :rowNumber="true"
          :rowSelection="true"
          @selectRowChange="handleSelectChange"
          :actionButton="true"
        />
      </a-tab-pane>

      <a-tab-pane tab="关联下级年度投资计划" :key="refKeys[2]" :forceRender="true">
        <j-editable-table
          :ref="refKeys[2]"
          :loading="investPlanSubRelationDetailTable.loading"
          :columns="investPlanSubRelationDetailTable.columns"
          :dataSource="investPlanSubRelationDetailTable.dataSource"
          :maxHeight="300"
          :rowNumber="true"
          :rowSelection="true"
          :actionButton="true"
        />
      </a-tab-pane>

      <a-tab-pane tab="附件列表" :key="refKeys[3]" :forceRender="true">
      <a-button type="primary" icon="snippets" @click="handleGd">归档</a-button>
        <j-editable-table
          :ref="refKeys[3]"
          :loading="fileListTable.loading"
          :columns="fileListTable.columns"
          :dataSource="fileListTable.dataSource"
          :maxHeight="300"
          :rowNumber="true"
          @selectRowChange="handleSelect"
          :rowSelection="true"
          :actionButton="true"
        />
      </a-tab-pane>
    </a-tabs>
    <a-modal
      title="从项目库导入"
      :visible="proVisible"
      :confirm-loading="confirmLoading"
      @ok="handleOkModal"
      width="1000px"
      @cancel="handleCancel"
    >
      <a-table
        :row-key="(record) => record.projectCode"
        :columns="relationColumns"
        :data-source="relationList"
        bordered
        :row-selection="rowSelection"
      >
      </a-table>
    </a-modal>
    <a-modal v-model="GdVisible" title="选择归档地址" @ok="handleOk2">
      <!-- <a-tree-select
        v-model="treeValue"
        style="width: 100%"
        :dropdown-style="{ maxHeight: '400px', overflow: 'auto' }"
        :tree-data="treeData"
        :load-data="onLoadData"
         placeholder="Please select"
      /> -->

      <j-category-select 
        pcode="B03"
        v-model="categroyUp"
        />
    </a-modal>
  </a-spin>
</template>

<script>
import JCategorySelect from '@/components/jeecg/JCategorySelect'
import JUpload from '@/components/jeecg/JUpload'
import { axios } from '@/utils/request'
import JYearPicker from '@/components/jeecg/JYearPicker'
import { FormTypes, getRefPromise } from '@/utils/JEditableTableUtil'
import { JEditableTableModelMixin } from '@/mixins/JEditableTableModelMixin'
import { validateDuplicateValue } from '@/utils/util'
import { VALIDATE_NO_PASSED, validateFormModelAndTables } from '@/utils/JEditableTableUtil'
import moment from 'moment'
export default {
  name: 'InvestPlanReportForml',
  mixins: [JEditableTableModelMixin],
  components: {
    JYearPicker,
    JUpload,
    JCategorySelect,
  },
  mounted() {
    this.$nextTick(()=>{
      this.fileListTable.dataSource = this.fileListTable.dataSource.map(item=>{
      return {
        ...item,
        fileName:item.fileUp
      }
    })
    })
  },
  data() {
    return {
      treeData:[],
      categroyUp:'',
      treeValue:'',
      GdVisible:false,
      keys: [],
      keys1: [],
      relationColumns: [
        {
          title: '#',
          dataIndex: '',
          key: 'rowIndex',
          width: 60,
          align: 'center',
          customRender: function (t, r, index) {
            return parseInt(index) + 1
          },
        },
        {
          title: '项目编码',
          align: 'center',
          key: 'projectCode',
          dataIndex: 'projectCode',
        },
        {
          title: '项目简称',
          align: 'center',
          key: 'projectShortName',
          dataIndex: 'projectShortName',
        },
        {
          title: '项目名称',
          align: 'center',
          key: 'projectName',
          dataIndex: 'projectName',
        },
        {
          title: '项目阶段',
          align: 'center',
          key: 'projectStatus',
          dataIndex: 'projectStatus',
          scopedSlots: { customRender: 'projectStatus' },
        },
        {
          title: '开工状态',
          align: 'center',
          key: 'startStatus',
          dataIndex: 'startStatus',
          scopedSlots: { customRender: 'startStatus' },
        },
        {
          title: '计划立项时间',
          align: 'center',
          key: 'projectInititationTime',
          dataIndex: 'projectInititationTime',
        },
        {
          title: '项目地址',
          align: 'center',
          dataIndex: 'projectAddr',
        },
        {
          title: '项目简介',
          align: 'center',
          dataIndex: 'projectDescription',
        },
        {
          title: '投资类别',
          align: 'center',
          key: 'investmentCategory',
          dataIndex: 'investmentCategory',
          scopedSlots: { customRender: 'investmentCategory' },
        },
      ],
      selectedRows: [],
      relationList: [],
      proVisible: false,
      confirmLoading: false,
      open: false,
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
      model: {},
      validatorRules: {},
      refKeys: ['investPlanReport', 'investPlanRelationDetail', 'investPlanSubRelationDetail', 'fileList'],
      tableKeys: ['investPlanRelationDetail', 'investPlanSubRelationDetail', 'fileList'],
      activeKey: 'investPlanReport',
      // 投资计划关联年度投资计划
      investPlanRelationDetailTable: {
        loading: false,
        dataSource: [],
        columns: [
          {
            title: '上报单位',
            key: 'reportUnitName',
            type: FormTypes.input,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
          {
            title: '上报单位ID',
            key: 'reportUnitId',
            type: FormTypes.input,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
          {
            title: '项目编码',
            key: 'projectCode',
            type: FormTypes.input,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
          {
            title: '项目阶段',
            key: 'projectState',
            type: FormTypes.input,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
          {
            title: '项目数量',
            key: 'projectCount',
            type: FormTypes.inputNumber,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
          {
            title: '投资类型',
            key: 'investmentType',
            type: FormTypes.input,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
          {
            title: '年度投资计划总额',
            key: 'estimatesInvestmentVolume',
            type: FormTypes.inputNumber,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
          {
            title: '版本号',
            key: 'planVersionNo',
            type: FormTypes.input,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
          {
            title: '编制状态',
            key: 'prereCodeStatus',
            type: FormTypes.input,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
          {
            title: '计划提交人',
            key: 'planSubmitterName',
            type: FormTypes.input,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
          {
            title: '计划提交时间',
            key: 'lastSubmitTime',
            type: FormTypes.input,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
          {
            title: '主表id',
            key: 'reportId',
            type: FormTypes.input,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
        ],
      },
      // 关联下级年度投资计划
      investPlanSubRelationDetailTable: {
        loading: false,
        dataSource: [],
        columns: [
          {
            title: '审批状态',
            key: 'state',
            type: FormTypes.input,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
          {
            title: '编号',
            key: 'code',
            type: FormTypes.input,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
          {
            title: '上报单位',
            key: 'reportUnitName',
            type: FormTypes.input,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
          {
            title: '计划总投资',
            key: 'planTotalAmount',
            type: FormTypes.inputNumber,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
          {
            title: '项目数',
            key: 'projectCount',
            type: FormTypes.inputNumber,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
          {
            title: '年度投资计划总额',
            key: 'yearPlanAmount',
            type: FormTypes.inputNumber,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
          {
            title: '上报时间',
            key: 'reportTime',
            type: FormTypes.input,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
          {
            title: '主表id',
            key: 'reportId',
            type: FormTypes.input,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
          {
            title: '计划id',
            key: 'planId',
            type: FormTypes.input,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
        ],
      },
      fileListTable: {
        loading: false,
        dataSource: [
          
        ],
        columns: [
          {
            title: '主表id',
            key: 'mainId',
            type: FormTypes.input,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
          {
            title: '文件上传',
            key: 'fileUp',
            type: FormTypes.file,
            token: true,
            responseName: 'message',
            width: '200px',
            placeholder: '请选择文件',
            defaultValue: '',
          },
          // {
          //   title: '归档地址id',
          //   key: 'categroyId',
          //   type: FormTypes.sel_search1,
          //   dictCode: 'B03',
          //   width: '200px',
          //   placeholder: '请输入${title}',
          //   defaultValue: '',
          // },
          {
            title: '归档地址名称',
            key: 'categroyName',
            type: FormTypes.input,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
          {
            title: '文件路径',
            key: 'filePath',
            type: FormTypes.input,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
          {
            title: '文件名称',
            key: 'fileName',
            type: FormTypes.input,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
        ],
      },
      url: {
        treeData:'/sys/category/loadTreeData',
        file:'/report/investPlanReport/placeToFile',
        notice: '/report/investPlanReport/noticeAll',
        list: '/investment/fixedAssetsInvestment/list',
        add: '/report/investPlanReport/add',
        edit: '/report/investPlanReport/edit',
        investPlanReport: {
          list: '/report/investPlanReport/queryById',
        },
        investPlanRelationDetail: {
          list: '/report/investPlanReport/queryInvestPlanRelationDetailByMainId',
        },
        investPlanSubRelationDetail: {
          list: '/report/investPlanReport/queryInvestPlanSubRelationDetailByMainId',
        },
        fileList: {
          list: '/report/investPlanReport/queryFileListByMainId',
        },
      },
    }
  },
  computed: {
    rowSelection() {
      return {
        onChange: (selectedRowKeys, selectedRows) => {
          this.selectedRows = selectedRows
          console.log(`selectedRowKeys: ${selectedRowKeys}`, 'selectedRows: ', selectedRows)
        },
      }
    },
  },
  methods: {
    onLoadData(treeNode){
      return new Promise(async (resolve) => {
        if (treeNode.dataRef.children) {
            resolve()
            return
          }
          let res = await axios.get(this.url.treeData,{params:{pcode:'B03',pid:treeNode.dataRef.key}})
          console.log('this',this)
          console.log('treeNode',treeNode)
          treeNode.dataRef.children = res.result
          this.treeData = [...this.treeData]
          resolve()
      });
    },
   async handleGd(){
      if(this.keys1.length){
        let result = await axios.get(this.url.treeData,{params:{pcode:'B03'}})
        this.treeData = result.result.map(item=>{
          return {
            ...item,
            value:item.code
          }
        })
        console.log(result)
        this.GdVisible = true
      }else{
        this.$message.warning('请选择要归档的文件')
      }
    },
    async handleOk2(){
      let fileIds = this.keys1.join()
      let result = await axios.get(this.url.file,{params:{fileIds,cateId:this.categroyUp}})
      this.GdVisible = false
    },
    handleSelect(keys){
      this.keys1 = keys
    },
    handleSelectChange(keys) {
      this.keys = keys
    },
    async handleNotice() {
      if (!this.keys.length) {
        return this.$message.warning('请选择项目')
      } else {
        let ids = this.keys.join()
        let result = await axios.post(this.url.notice + '?projectIds=' + ids)
        if (result.code === 200) {
          return this.$message.success('通知成功')
        }
        console.log(result)
      }
    },
    //获取关联列表
    async getList() {
      let result = await axios.get(this.url.list)
      this.relationList = result.result.records
    },
    handleOkModal(e) {
      this.confirmLoading = true
      setTimeout(() => {
        this.investPlanRelationDetailTable.dataSource = this.selectedRows.map((item) => {
          return {
            ...item,
            projectId: item.id,
          }
        })
        this.proVisible = false
        this.confirmLoading = false
      }, 1000)
    },
    handleCancel(e) {
      console.log('Clicked cancel button')
      this.proVisible = false
    },
    handleRelated() {
      this.getList()
      this.proVisible = true
    },
    openChange(val) {
      if (val) {
        this.open = true
      } else {
        this.open = false
      }
    },
    panelChange(val) {
      this.time3 = moment(val).format('YYYY')
      this.open = false
      //调取接口获取数据
    },
    getAllTable() {
      let values = this.tableKeys.map((key) => getRefPromise(this, key))
      return Promise.all(values)
    },
    /** 调用完edit()方法之后会自动调用此方法 */
    editAfter() {
      this.$nextTick(() => {})
      // 加载子表数据
      if (this.model.id) {
        let params = { id: this.model.id }
        this.requestSubTableData(this.url.investPlanRelationDetail.list, params, this.investPlanRelationDetailTable)
        this.requestSubTableData(
          this.url.investPlanSubRelationDetail.list,
          params,
          this.investPlanSubRelationDetailTable
        )
        this.requestSubTableData(this.url.fileList.list, params, this.fileListTable)

      }
    },
    //校验所有一对一子表表单
    validateSubForm(allValues) {
      return new Promise((resolve, reject) => {
        Promise.all([])
          .then(() => {
            resolve(allValues)
          })
          .catch((e) => {
            reject(e)
          })
      })
    },
    /** 整理成formData */
    classifyIntoFormData(allValues) {
      console.log('allValues', allValues)
      let main = Object.assign(this.model, allValues.formValue)
      return {
        ...main, // 展开
        investPlanRelationDetailList: allValues.tablesValue[0].values,
        investPlanSubRelationDetailList: allValues.tablesValue[1].values,
        fileListList: allValues.tablesValue[2].values.map((item) => {
          return {
            ...item,
            mainId: this.model.id,
          }
        }),
      }
    },
    /** 确定按钮点击事件 */
    handleOk() {
      /** 触发表单验证 */
      this.getAllTable()
        .then((tables) => {
          return validateFormModelAndTables(this.$refs.form, this.model, tables)
        })
        .then((allValues) => {
          console.log(allValues)
          /** 一次性验证一对一的所有子表 */
          return this.validateSubForm(allValues)
        })
        .then((allValues) => {
          if (typeof this.classifyIntoFormData !== 'function') {
            throw this.throwNotFunction('classifyIntoFormData')
          }
          console.log('this.classifyIntoFormData', typeof this.classifyIntoFormData)
          let formData = this.classifyIntoFormData(allValues)
          console.log('formData', formData)
          // 发起请求
          return this.request(formData)
        })
        .catch((e) => {
          if (e.error === VALIDATE_NO_PASSED) {
            // 如果有未通过表单验证的子表，就自动跳转到它所在的tab
            this.activeKey = e.index == null ? this.refKeys[0] : this.refKeys[e.index + 1]
          } else {
            console.error(e)
          }
        })
    },
    validateError(msg) {
      this.$message.error(msg)
    },
    close() {
      this.visible = false
      this.$emit('close')
      this.$refs.form.clearValidate()
    },
  },
}
</script>

<style scoped lang="less">
/** tab panel 中有下拉框/日期 这类带下拉效果的，需要加此样式 */
/deep/ .jeecg-tabs-top {
  overflow: visible;
}
</style>