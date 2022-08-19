<template>
  <a-spin :spinning="confirmLoading">
    <div class="title">
      <span>项目入库</span>
      <a-button type="primary" v-if="activeKey === 'fixedAssetsInvestment'" @click="showAll">
        {{ this.showArea || this.showInfo || this.showCategory ? '全部收缩' : '全部展开' }}</a-button
      >
    </div>
    <a-row class="mt20">
      <a-input disabled :value="model.projectName" />
    </a-row>
    <a-tabs v-model="activeKey" @change="handleChangeTabs">
      <!--主表区域 -->
      <a-tab-pane
        tab="固定资产投资表"
        :key="refKeys[0]"
        :forceRender="true"
        :class="'jeecg-tabs-top'"
        :animated="false"
      >
        <a-form-model ref="form" :model="model" :rules="validatorRules">
          <a-row>
            <a-col :xs="24" :sm="12">
              <a-form-model-item label="项目编码" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="projectCode">
                <a-input v-model="model.projectCode" placeholder="请输入项目编码" disabled></a-input>
              </a-form-model-item>
            </a-col>
            <a-col :xs="24" :sm="12">
              <a-form-model-item label="项目简称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="projectShortName">
                <a-input v-model="model.projectShortName" placeholder="请输入项目简称"></a-input>
              </a-form-model-item>
            </a-col>
            <a-col :xs="24" :sm="12">
              <a-form-model-item label="项目名称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="projectName">
                <a-input v-model="model.projectName" placeholder="请输入项目名称"></a-input>
              </a-form-model-item>
            </a-col>
            <a-col :xs="24" :sm="12">
              <a-form-model-item label="项目阶段" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="projectStatus">
                <j-dict-select-tag
                  type="list"
                  v-model="model.projectStatus"
                  dictCode="project_status"
                  placeholder="请选择项目阶段"
                  disabled
                />
              </a-form-model-item>
            </a-col>
            <a-col :xs="24" :sm="12">
              <a-form-model-item label="开工状态" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="startStatus">
                <j-dict-select-tag
                  type="list"
                  v-model="model.startStatus"
                  dictCode="project_start_status"
                  placeholder="请选择开工状态"
                />
              </a-form-model-item>
            </a-col>
            <a-col :xs="24" :sm="12">
              <a-form-model-item label="预计开始时间" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="preStartTime">
                <j-date
                  placeholder="请选择预计开始时间"
                  v-model="model.preStartTime"
                  :show-time="true"
                  date-format="YYYY-MM-DD HH:mm:ss"
                  style="width: 100%"
                />
              </a-form-model-item>
            </a-col>
            <a-col :xs="24" :sm="12">
              <a-form-model-item
                label="预计结束时间"
                :labelCol="labelCol"
                :wrapperCol="wrapperCol"
                prop="preFinishTime"
              >
                <j-date
                  placeholder="请选择预计结束时间"
                  v-model="model.preFinishTime"
                  :show-time="true"
                  date-format="YYYY-MM-DD HH:mm:ss"
                  style="width: 100%"
                />
              </a-form-model-item>
            </a-col>
            <a-col :span="12" >
              <a-form-model-item label="项目图片" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="picFile">
                <j-image-upload isMultiple  v-model="model.picFile" ></j-image-upload>
              </a-form-model-item>
            </a-col>
            <a-col :span="24" class="shrink">
              <span @click="changeArea('area')">
                <img src="../../../assets/img/up.png" v-if="showArea" />
                <img src="../../../assets/img/down.png" alt="" v-else />
                <span>国家或地区信息</span>
              </span>
            </a-col>
            <div v-show="showArea">
              <a-col :xs="24" :sm="12">
                <a-form-model-item label="国家地区" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="area">
                  <a-input v-model="model.area" placeholder="请输入国家地区"></a-input>
                </a-form-model-item>
              </a-col>
            </div>
            <a-col :span="24" class="shrink">
              <span @click="changeArea('info')">
                <img src="../../../assets/img/up.png" v-if="showInfo" />
                <img src="../../../assets/img/down.png" alt="" v-else />
                <span>详情信息</span>
              </span>
            </a-col>
            <div v-show="showInfo">
              <a-col :xs="24" :sm="12">
                <a-form-model-item label="经度" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="longitude">
                  <a-input-number v-model="model.longitude" placeholder="请输入经度" style="width: 100%" />
                </a-form-model-item>
              </a-col>
              <a-col :xs="24" :sm="12">
                <a-form-model-item label="纬度" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="latitude">
                  <a-input-number v-model="model.latitude" placeholder="请输入纬度" style="width: 100%" />
                </a-form-model-item>
              </a-col>
              <a-col :xs="24" :sm="12">
                <a-form-model-item label="责任人" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="personInCharge">
                  <j-select-user-by-dep v-model="model.personInCharge" :multi="true" />
                </a-form-model-item>
              </a-col>
              <a-col :xs="24" :sm="12">
                <a-form-model-item
                  label="计划立项时间"
                  :labelCol="labelCol"
                  :wrapperCol="wrapperCol"
                  prop="projectInititationTime"
                >
                  <j-date
                    placeholder="请选择计划立项时间"
                    v-model="model.projectInititationTime"
                    :show-time="true"
                    date-format="YYYY-MM-DD HH:mm:ss"
                    style="width: 100%"
                  />
                </a-form-model-item>
              </a-col>
              <a-col :xs="24" :sm="12">
                <a-form-model-item label="项目地址" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="projectAddr">
                  <a-input v-model="model.projectAddr" placeholder="请输入项目地址"></a-input>
                </a-form-model-item>
              </a-col>
              <a-col :xs="24" :sm="12">
                <a-form-model-item
                  label="项目决策单位"
                  :labelCol="labelCol"
                  :wrapperCol="wrapperCol"
                  prop="projectDecision"
                >
                  <j-select-depart v-model="model.projectDecision" :multi="true" />
                </a-form-model-item>
              </a-col>
              <a-col :xs="24" :sm="12">
                <a-form-model-item
                  label="项目实施单位/部门"
                  :labelCol="labelCol"
                  :wrapperCol="wrapperCol"
                  prop="projectConstruction"
                >
                  <j-select-depart v-model="model.projectConstruction" :multi="true" />
                </a-form-model-item>
              </a-col>
              <a-col :xs="24" :sm="12">
                <a-form-model-item
                  label="项目主管部门"
                  :labelCol="labelCol"
                  :wrapperCol="wrapperCol"
                  prop="projectCharge"
                >
                  <j-select-depart v-model="model.projectCharge" :multi="true" />
                </a-form-model-item>
              </a-col>
              <a-col :xs="24" :sm="12">
                <a-form-model-item
                  label="项目简介"
                  :labelCol="labelCol"
                  :wrapperCol="wrapperCol"
                  prop="projectDescription"
                >
                  <a-textarea v-model="model.projectDescription" rows="4" placeholder="请输入项目简介" />
                </a-form-model-item>
              </a-col>
            </div>
            <a-col :span="24" class="shrink">
              <span @click="changeArea('category')">
                <img src="../../../assets/img/up.png" v-if="showCategory" />
                <img src="../../../assets/img/down.png" alt="" v-else />
                <span>分类信息</span>
              </span>
            </a-col>
            <div v-show="showCategory">
              <a-col :xs="24" :sm="12">
                <a-form-model-item
                  label="投资类别"
                  :labelCol="labelCol"
                  :wrapperCol="wrapperCol"
                  prop="investmentCategory"
                >
                  <j-dict-select-tag
                    type="list"
                    v-model="model.investmentCategory"
                    dictCode="investment_type"
                    placeholder="请选择投资类别"
                  />
                </a-form-model-item>
              </a-col>
              <a-col :span="12" >
                <a-form-model-item label="已完成工作" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="alreadyWork">
                  <j-select-multiple type="list" v-model="model.alreadyWork" dictCode="already_work" placeholder="请选择已完成工作" />
                </a-form-model-item>
              </a-col>
              <a-col :xs="24" :sm="12">
                <a-form-model-item
                  label="是否对外投资"
                  :labelCol="labelCol"
                  :wrapperCol="wrapperCol"
                  prop="extProjetFlag"
                >
                  <j-dict-select-tag
                    type="radio"
                    v-model="model.extProjetFlag"
                    dictCode="ture_or_false"
                    placeholder="请选择是否对外投资"
                  />
                </a-form-model-item>
              </a-col>
              <a-col :xs="24" :sm="24"  v-if="model.investmentCategory == '2'">
                <a-form-model-item
                  label="重点项目类型"
                  :labelCol="{ span: 3 }"
                  :wrapperCol="wrapperCol"
                  prop="keyProjectType"
                >
                  <j-multi-select-tag
                    type="checkbox"
                    v-model="model.keyProjectType"
                    dictCode="key_project_type"
                    placeholder="请选择重点项目类型"
                  />
                </a-form-model-item>
              </a-col>
            </div>
          </a-row>
        </a-form-model>
      </a-tab-pane>
      <!--子表单区域 -->
      <a-tab-pane tab="关联信息" :key="refKeys[1]" :forceRender="true">
        <a-button type="primary" @click="showRelated">
          关联
        </a-button>
        <j-editable-table
          :ref="refKeys[1]"
          :loading="fixedAssetsInvestmentSubTable.loading"
          :columns="fixedAssetsInvestmentSubTable.columns"
          :dataSource="fixedAssetsInvestmentSubTable.dataSource"
          :maxHeight="300"
          :rowNumber="true"
          :rowSelection="true"
          :actionButton="true"
        />
      </a-tab-pane>

      <a-tab-pane tab="固定资产投资表日志" :key="refKeys[2]" :forceRender="true">
        <j-editable-table
          :ref="refKeys[2]"
          :loading="fixedAssetsInvestmentLogTable.loading"
          :columns="fixedAssetsInvestmentLogTable.columns"
          :dataSource="fixedAssetsInvestmentLogTable.dataSource"
          :maxHeight="300"
          :rowNumber="true"
          :rowSelection="true"
          :actionButton="true"
        />
      </a-tab-pane>
    </a-tabs>
    <a-modal v-model:visible="visibleShowRelated" title="关联项目" width="1000px" @ok="handleOkRelation">
      <a-table
        :row-key="(record) => record.projectCode"
        :columns="columns"
        :data-source="relationList"
        bordered
        :row-selection="rowSelection"
      >
      </a-table>
    </a-modal>
  </a-spin>
</template>

<script>
import { axios } from '@/utils/request'
import { FormTypes, getRefPromise } from '@/utils/JEditableTableUtil'
import { JEditableTableModelMixin } from '@/mixins/JEditableTableModelMixin'
import { validateDuplicateValue } from '@/utils/util'
import { VALIDATE_NO_PASSED, validateFormModelAndTables } from '@/utils/JEditableTableUtil'
import JSelectMultiple from '../../../components/jeecg/JSelectMultiple.vue'

export default {
  name: 'FixedAssetsInvestmentForml',
  mixins: [JEditableTableModelMixin],
  components: {JSelectMultiple},
  data() {
    return {
      allOpen: true,
      selectedRows: [],
      visibleShowRelated: false,
      relationList: [],
      columns: [
        {
          title: '状态',
          dataIndex: 'stg',
          key: 'stg',
        },
        {
          title: '项目编码',
          dataIndex: 'projectCode',
          key: 'projectCode',
        },
        {
          title: '项目名称',
          dataIndex: 'projectName',
          key: 'projectName',
        },
        {
          title: '开始时间',
          dataIndex: 'startTime',
          key: 'startTime',
        },
        {
          title: '登记人',
          dataIndex: 'registrant',
          key: 'registrant',
        },
      ],
      showArea: true,
      showInfo: true,
      showCategory: true,
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
      model: {
        investmentCategory:1,
        projectStatus: 0,
      },
      validatorRules: {
        projectName: [
          {
            required: true,
            message: '请输入项目名称',
            trigger: 'blur',
          },
        ],
      },
      refKeys: ['fixedAssetsInvestment', 'fixedAssetsInvestmentSub', 'fixedAssetsInvestmentLog'],
      tableKeys: ['fixedAssetsInvestmentSub', 'fixedAssetsInvestmentLog'],
      activeKey: 'fixedAssetsInvestment',
      // 关联信息
      fixedAssetsInvestmentSubTable: {
        loading: false,
        dataSource: [],
        columns: [
          {
            title: '主表ID',
            key: 'investmentId',
            type: FormTypes.input,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
          {
            title: '状态',
            key: 'projectStatus',
            type: FormTypes.input,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
          {
            title: '项目编码',
            key: 'perojectCode',
            type: FormTypes.input,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
          {
            title: '项目名称',
            key: 'projectName',
            type: FormTypes.input,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
          {
            title: '修改人',
            key: 'projectUpdateBy',
            type: FormTypes.input,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
          {
            title: '修改日期',
            key: 'projectUpdateTime',
            type: FormTypes.datetime,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
        ],
      },
      // 固定资产投资表日志
      fixedAssetsInvestmentLogTable: {
        loading: false,
        dataSource: [],
        columns: [
          {
            title: '操作人',
            key: 'operationUser',
            type: FormTypes.input,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
          {
            title: '操作',
            key: 'operation',
            type: FormTypes.input,
            width: '200px',
            placeholder: '请输入${title}',
            defaultValue: '',
          },
        ],
      },
      url: {
        list: '/investment/fixedAssetsInvestment/list',
        projectCode: '/sys/fillRule/executeRuleByCode/project_code',
        add: '/investment/fixedAssetsInvestment/add',
        edit: '/investment/fixedAssetsInvestment/edit',
        fixedAssetsInvestment: {
          list: '/investment/fixedAssetsInvestment/queryById',
        },
        fixedAssetsInvestmentSub: {
          list: '/investment/fixedAssetsInvestment/queryFixedAssetsInvestmentSubByMainId',
        },
        fixedAssetsInvestmentLog: {
          list: '/investment/fixedAssetsInvestment/queryFixedAssetsInvestmentLogByMainId',
        },
      },
    }
  },
  created() {
    this.getProjectCode()
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
    //获取项目编码
    async getProjectCode() {
      let result = await axios.put(this.url.projectCode, {})
      this.model.projectCode = result.result
    },
    //获取关联列表
    async getList() {
      let result = await axios.get(this.url.list)
      this.relationList = result.result.records
    },
    //关联弹框确定的回调
    handleOkRelation() {
      this.fixedAssetsInvestmentSubTable.dataSource = this.selectedRows
      this.visibleShowRelated = false
    },
    //关联按钮的回调
    showRelated() {
      this.visibleShowRelated = true
      this.getList()
    },
    //全部展开/收缩
    showAll() {
      if (this.showArea || this.showInfo || this.showCategory) {
        this.showArea = false
        this.showInfo = false
        this.showCategory = false
        return true
      } else {
        this.showArea = true
        this.showInfo = true
        this.showCategory = true
        return false
      }
    },
    //展开与隐藏
    changeArea(show) {
      switch (show) {
        case 'area':
          this.showArea = !this.showArea
          break
        case 'info':
          this.showInfo = !this.showInfo
          break
        case 'category':
          this.showCategory = !this.showCategory
          break
      }
    },
    getAllTable() {
      let values = this.tableKeys.map((key) => getRefPromise(this, key))
      return Promise.all(values)
    },
    async getProjectCode() {
      let result = await axios.put(this.url.projectCode, {})
      this.model.projectCode = result.result
    },
    /** 调用完edit()方法之后会自动调用此方法 */
    editAfter() {
      this.$nextTick(() => {})
      // 加载子表数据
      if (this.model.id) {
        let params = { id: this.model.id }
        this.requestSubTableData(this.url.fixedAssetsInvestmentSub.list, params, this.fixedAssetsInvestmentSubTable)
        this.requestSubTableData(this.url.fixedAssetsInvestmentLog.list, params, this.fixedAssetsInvestmentLogTable)
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
      let main = Object.assign(this.model, allValues.formValue)
      return {
        ...main, // 展开
        fixedAssetsInvestmentSubList: allValues.tablesValue[0].values,
        fixedAssetsInvestmentLogList: allValues.tablesValue[1].values,
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
          /** 一次性验证一对一的所有子表 */
          return this.validateSubForm(allValues)
        })
        .then((allValues) => {
          if (typeof this.classifyIntoFormData !== 'function') {
            throw this.throwNotFunction('classifyIntoFormData')
          }
          console.log('this.classifyIntoFormData', typeof this.classifyIntoFormData)
          let formData = this.classifyIntoFormData(allValues)

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
::v-deep .jeecg-tabs-top {
  overflow: visible;
}
.mt20 {
  margin-top: 20px;
}
.title {
  width: 90%;
  display: flex;
  justify-content: space-between;
}
.shrink {
  height: 40px;
  line-height: 40px;
  font-weight: 900;
  font-size: 16px;
  padding-left: 20px;
  background: #f4f9fa;
  border-bottom: 1px solid #999;
  img {
    margin-right: 10px;
  }
}
::v-deep .ant-spin-container {
  .ant-row-flex {
    // display: none;
    .ant-col {
      .action-button {
        .ant-btn-group {
          button {
            display: none;
          }
        }
      }
    }
  }
}
</style>