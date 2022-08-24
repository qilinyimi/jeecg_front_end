<template>
  <div class="FixedAssetsInvestmentList">
    <div class="tree">
      <a-tree @select="handleSelect" :tree-data="treeData"></a-tree>
    </div>
    <a-card :bordered="false">
      <!-- 查询区域 -->
      <div class="table-page-search-wrapper">
        <a-form layout="inline" @keyup.enter.native="searchQuery">
          <a-row :gutter="24"> </a-row>
        </a-form>
      </div>
      <!-- 查询区域-END -->

      <!-- 操作按钮区域 -->
      <div class="table-operator">
        <a-popover title="自定义列" trigger="click" placement="leftBottom">
          <template slot="content">
            <a-checkbox-group
              style="width: 130px"
              @change="onColSettingsChange"
              v-model="settingColumns"
              :defaultValue="settingColumns"
              :options="plainOptions"
            >
            </a-checkbox-group>
          </template>
          <a-button type="primary">设置</a-button>
        </a-popover>
        <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>
        <a-button type="primary" icon="download" @click="handleExportXls('固定资产投资表')">导出</a-button>
        <a-upload
          name="file"
          :showUploadList="false"
          :multiple="false"
          :headers="tokenHeader"
          :action="importExcelUrl"
          @change="handleImportExcel"
        >
          <a-button type="primary" icon="import">导入</a-button>
        </a-upload>
        <!-- 高级查询区域 -->
        <j-super-query
          :fieldList="superFieldList"
          ref="superQueryModal"
          @handleSuperQuery="handleSuperQuery"
        ></j-super-query>
        <a-dropdown v-if="selectedRowKeys.length > 0">
          <a-menu slot="overlay">
            <a-menu-item key="1" @click="batchDel"><a-icon type="delete" />删除</a-menu-item>
          </a-menu>
          <a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>
        </a-dropdown>
      </div>

      <!-- table区域-begin -->
      <div>
        <div class="ant-alert ant-alert-info" style="margin-bottom: 16px">
          <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择
          <a style="font-weight: 600">{{ selectedRowKeys.length }}</a
          >项
          <a style="margin-left: 24px" @click="onClearSelected">清空</a>
        </div>

        <a-table
          ref="table"
          size="middle"
          bordered
          rowKey="id"
          class="j-table-force-nowrap"
          :scroll="{ x: true }"
          :columns="columns"
          :customRow="handleDbclick"
          :dataSource="dataSource"
          :pagination="ipagination"
          :loading="loading"
          :rowSelection="{ selectedRowKeys: selectedRowKeys, onChange: onSelectChange }"
          @change="handleTableChange"
        >
          <template slot="htmlSlot" slot-scope="text">
            <div v-html="text"></div>
          </template>
          <template slot="imgSlot" slot-scope="text, record">
            <span v-if="!text" style="font-size: 12px; font-style: italic">无图片</span>
            <img
              v-else
              :src="getImgView(text)"
              :preview="record.id"
              height="25px"
              alt=""
              style="max-width: 80px; font-size: 12px; font-style: italic"
            />
          </template>
          <template slot="projectStatus" slot-scope="text, record">
            {{text == '0' ? '入库' : text == '1' ? '立项' : ''}}
          </template>
          <template slot="startStatus" slot-scope="text, record">
            {{text == '0' ? '未开工' : text == '1' ? '已开工' : ''}}
          </template>
          <template slot="investmentCategory" slot-scope="text, record">
            {{text == '1' ? '固定资产' : text == '2' ? '股权资产' : ''}}
          </template>
          <template slot="fileSlot" slot-scope="text">
            <span v-if="!text" style="font-size: 12px; font-style: italic">无文件</span>
            <a-button v-else :ghost="true" type="primary" icon="download" size="small" @click="uploadFile(text)">
              下载
            </a-button>
          </template>

          <span slot="action" slot-scope="text, record">
            <a @click="handleEdit(record)">编辑</a>

            <a-divider type="vertical" />
            <a-dropdown>
              <a class="ant-dropdown-link">更多 <a-icon type="down" /></a>
              <a-menu slot="overlay">
                <a-menu-item>
                  <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
                    <a>删除</a>
                  </a-popconfirm>
                </a-menu-item>
              </a-menu>
            </a-dropdown>
          </span>
        </a-table>
      </div>

      <fixedAssetsInvestment-modal ref="modalForm" @ok="modalFormOk"></fixedAssetsInvestment-modal>
    </a-card>
  </div>
</template>

<script>
import { axios } from '@/utils/request'
import { JeecgListMixin } from '@/mixins/JeecgListMixin'
import FixedAssetsInvestmentModal from './modules/FixedAssetsInvestmentModal'
import { filterMultiDictText } from '@/components/dict/JDictSelectUtil'
import '@/assets/less/TableExpand.less'

export default {
  name: 'FixedAssetsInvestmentList',
  mixins: [JeecgListMixin],
  components: {
    FixedAssetsInvestmentModal,
  },
  data() {
    return {
      plainOptions: [
        {
          label: '项目编码',
          value: 'projectCode',
        },
        {
          label: '项目简称',
          value: 'projectShortName',
        },
        {
          label: '项目名称',
          value: 'projectName',
        },
        {
          label: '项目阶段',
          value: 'projectStatus',
        },
        {
          label: '开工状态',
          value: 'startStatus',
        },
        {
          label: '计划立项时间',
          value: 'projectInititationTime',
        },
        {
          label: '项目地址',
          value: 'projectAddr',
        },
        {
          label: '项目简介',
          value: 'projectDescription',
        },
        {
          label: '投资类别',
          value: 'investmentCategory',
        },
      ],
      settingColumns: [
        'projectCode',
        'projectShortName',
        'projectName',
        'projectStatus',
        'startStatus',
        'projectInititationTime',
        'projectAddr',
        'projectDescription',
        'investmentCategory',
      ],
      treeData: [],
      description: '固定资产投资表管理页面',
      // 表头
      intColumns: [
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
          dataIndex: 'projectCode',
        },
        {
          title: '项目简称',
          align: 'center',
          dataIndex: 'projectShortName',
        },
        {
          title: '项目名称',
          align: 'center',
          dataIndex: 'projectName',
        },
        {
          title: '项目阶段',
          align: 'center',
          dataIndex: 'projectStatus',
        },
        {
          title: '开工状态',
          align: 'center',
          dataIndex: 'startStatus',
        },
        {
          title: '计划立项时间',
          align: 'center',
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
          dataIndex: 'investmentCategory',
        },
        {
          title: '操作',
          dataIndex: 'action',
          align: 'center',
          fixed: 'right',
          width: 147,
          scopedSlots: { customRender: 'action' },
        },
      ],
      columns: [
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
          key:'projectCode',
          dataIndex: 'projectCode',
        },
        {
          title: '项目简称',
          align: 'center',
          key:'projectShortName',
          dataIndex: 'projectShortName',
        },
        {
          title: '项目名称',
          align: 'center',
          key:'projectName',
          dataIndex: 'projectName',
        },
        {
          title: '项目阶段',
          align: 'center',
          key:'projectStatus',
          dataIndex: 'projectStatus',
          scopedSlots: { customRender: 'projectStatus' },
        },
        {
          title: '开工状态',
          align: 'center',
          key:'startStatus',
          dataIndex: 'startStatus',
          scopedSlots: { customRender: 'startStatus' },
        },
        {
          title: '计划立项时间',
          align: 'center',
          key:'projectInititationTime',
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
          key:'investmentCategory',
          dataIndex: 'investmentCategory',
          scopedSlots: { customRender: 'investmentCategory' },

        },
        {
          title: '操作',
          dataIndex: 'action',
          align: 'center',
          fixed: 'right',
          width: 147,
          scopedSlots: { customRender: 'action' },
        },
      ],
      url: {
        tree: '/sys/sysDepart/queryTreeList',
        list: '/investment/fixedAssetsInvestment/list',
        delete: '/investment/fixedAssetsInvestment/delete',
        deleteBatch: '/investment/fixedAssetsInvestment/deleteBatch',
        exportXlsUrl: '/investment/fixedAssetsInvestment/exportXls',
        importExcelUrl: 'investment/fixedAssetsInvestment/importExcel',
      },
      dictOptions: {},
      superFieldList: [],
    }
  },
  created() {
    this.getSuperFieldList()
    this.getTree()
  },
  computed: {
    importExcelUrl: function () {
      return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`
    },
  },
  methods: {
    handleDbclick(record){
      return {
        on:{
          dblclick:(event)=>{
            console.log(record)
            this.$message.success('双击表格事件触发')
          },
        }
      }
    },
    //点击树节点的回调
    async handleSelect(selectedKeys,e){
      console.log(selectedKeys,e)
      let result = await axios.get(this.url.list + '?curSysOrgCode=' + e.node.dataRef.orgCode)
      this.dataSource = result.result.records
      console.log(result)
    },
    onColSettingsChange(checkedValues) {
      this.settingColumns = checkedValues
      console.log(this.columns)
      this.columns = this.intColumns.filter((item) => {
        if (item.key == 'rowIndex' || item.dataIndex == 'action') {
          return true
        }
        if (this.settingColumns.includes(item.dataIndex)) {
          return true
        }
        return false
      })
      console.log(this.columns)
    },
    async getTree() {
      let result = await axios.get(this.url.tree)
      this.treeData = result.result
    },
    initDictConfig() {},
    getSuperFieldList() {
      let fieldList = []
      fieldList.push({ type: 'string', value: 'projectCode', text: '项目编码', dictCode: '' })
      fieldList.push({ type: 'string', value: 'projectShortName', text: '项目简称', dictCode: '' })
      fieldList.push({ type: 'string', value: 'projectName', text: '项目名称', dictCode: '' })
      fieldList.push({ type: 'int', value: 'projectStatus', text: '项目阶段', dictCode: 'project_status' })
      fieldList.push({ type: 'string', value: 'startStatus', text: '开工状态', dictCode: 'project_start_status' })
      fieldList.push({ type: 'datetime', value: 'preStartTime', text: '预计开始时间' })
      fieldList.push({ type: 'datetime', value: 'preFinishTime', text: '预计结束时间' })
      fieldList.push({ type: 'string', value: 'area', text: '国家地区', dictCode: '' })
      fieldList.push({ type: 'double', value: 'longitude', text: '经度', dictCode: '' })
      fieldList.push({ type: 'double', value: 'latitude', text: '纬度', dictCode: '' })
      fieldList.push({ type: 'sel_user', value: 'personInCharge', text: '责任人' })
      fieldList.push({ type: 'datetime', value: 'projectInititationTime', text: '计划立项时间' })
      fieldList.push({ type: 'string', value: 'projectAddr', text: '项目地址', dictCode: '' })
      fieldList.push({ type: 'sel_depart', value: 'projectDecision', text: '项目决策单位' })
      fieldList.push({ type: 'sel_depart', value: 'projectConstruction', text: '项目实施单位/部门' })
      fieldList.push({ type: 'sel_depart', value: 'projectCharge', text: '项目主管部门' })
      fieldList.push({ type: 'string', value: 'projectDescription', text: '项目简介', dictCode: '' })
      fieldList.push({ type: 'int', value: 'investmentCategory', text: '投资类别', dictCode: 'investment_type' })
      fieldList.push({ type: 'string', value: 'extProjetFlag', text: '是否对外投资', dictCode: 'ture_or_false' })
      fieldList.push({ type: 'string', value: 'keyProjectType', text: '重点项目类型', dictCode: 'key_project_type' })
      this.superFieldList = fieldList
    },
  },
}
</script>
<style scoped lang="less">
@import '~@assets/less/common.less';
.FixedAssetsInvestmentList {
  display: flex;
  justify-content: space-between;
  .tree {
    height: 100vh;
    background: #fff;
    border-right: 3px solid #ccc;
    width: 260px;
  }
  .ant-tree {
  }
  .ant-card {
    flex: 1;
  }
}
</style>