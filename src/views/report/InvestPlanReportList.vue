<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
        </a-row>
      </a-form>
    </div>
    <!-- 查询区域-END -->
    
    <!-- 操作按钮区域 -->
    <div class="table-operator">
      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>
      <a-button type="primary" icon="download" @click="handleExportXls('投资计划上报')">导出</a-button>
      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">
        <a-button type="primary" icon="import">导入</a-button>
      </a-upload>
      <!-- 高级查询区域 -->
      <j-super-query :fieldList="superFieldList" ref="superQueryModal" @handleSuperQuery="handleSuperQuery"></j-super-query>
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>
      </a-dropdown>
    </div>

    <!-- table区域-begin -->
    <div>
      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择 <a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项
        <a style="margin-left: 24px" @click="onClearSelected">清空</a>
      </div>

      <a-table
        ref="table"
        size="middle"
        bordered
        rowKey="id"
        class="j-table-force-nowrap"
        :scroll="{x:true}"
        :columns="columns"
        :dataSource="dataSource"
        :pagination="ipagination"
        :loading="loading"
        :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
        @change="handleTableChange">

        <template slot="htmlSlot" slot-scope="text">
          <div v-html="text"></div>
        </template>
        <template slot="imgSlot" slot-scope="text,record">
          <span v-if="!text" style="font-size: 12px;font-style: italic;">无图片</span>
          <img v-else :src="getImgView(text)" :preview="record.id" height="25px" alt="" style="max-width:80px;font-size: 12px;font-style: italic;"/>
        </template>
        <template slot="fileSlot" slot-scope="text">
          <span v-if="!text" style="font-size: 12px;font-style: italic;">无文件</span>
          <a-button
            v-else
            :ghost="true"
            type="primary"
            icon="download"
            size="small"
            @click="uploadFile(text)">
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

    <investPlanReport-modal ref="modalForm" @ok="modalFormOk"></investPlanReport-modal>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import InvestPlanReportModal from './modules/InvestPlanReportModal'
  import '@/assets/less/TableExpand.less'

  export default {
    name: "InvestPlanReportList",
    mixins:[JeecgListMixin],
    components: {
      InvestPlanReportModal
    },
    data () {
      return {
        description: '投资计划上报管理页面',
        // 表头
        columns: [
          {
            title: '#',
            dataIndex: '',
            key:'rowIndex',
            width:60,
            align:"center",
            customRender:function (t,r,index) {
              return parseInt(index)+1;
            }
          },
          {
            title:'状态名称',
            align:"center",
            dataIndex: 'stateName'
          },
          {
            title:'状态编码',
            align:"center",
            dataIndex: 'stateCode'
          },
          {
            title:'编号',
            align:"center",
            dataIndex: 'code'
          },
          {
            title:'上报单位',
            align:"center",
            dataIndex: 'reportUnitName'
          },
          {
            title:'上报年度',
            align:"center",
            dataIndex: 'year'
          },
          {
            title:'版本号',
            align:"center",
            dataIndex: 'versionNo'
          },
          {
            title:'项目数',
            align:"center",
            dataIndex: 'projectCount'
          },
          {
            title:'提交人',
            align:"center",
            dataIndex: 'submitterId'
          },
          {
            title:'提交人Name',
            align:"center",
            dataIndex: 'submitterName'
          },
          {
            title:'提交时间',
            align:"center",
            dataIndex: 'submitTime'
          },
          {
            title:'本级投资总资金',
            align:"center",
            dataIndex: 'currentAmount'
          },
          {
            title:'下级投资总资金',
            align:"center",
            dataIndex: 'lowerAmount'
          },
          {
            title:'投资总资金',
            align:"center",
            dataIndex: 'totalAmount'
          },
          {
            title:'登记人ID',
            align:"center",
            dataIndex: 'creatorId'
          },
          {
            title:'登记人Name',
            align:"center",
            dataIndex: 'creatorName'
          },
          {
            title:'修改人ID',
            align:"center",
            dataIndex: 'modifierId'
          },
          {
            title:'修改人Name',
            align:"center",
            dataIndex: 'modifierName'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            fixed:"right",
            width:147,
            scopedSlots: { customRender: 'action' },
          }
        ],
        url: {
          list: "/report/investPlanReport/list",
          delete: "/report/investPlanReport/delete",
          deleteBatch: "/report/investPlanReport/deleteBatch",
          exportXlsUrl: "/report/investPlanReport/exportXls",
          importExcelUrl: "report/investPlanReport/importExcel",
        },
        dictOptions:{},
        superFieldList:[],
      }
    },
    created() {
      this.getSuperFieldList();
    },
    computed: {
      importExcelUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      }
    },
    methods: {
      initDictConfig(){
      },
      getSuperFieldList(){
        let fieldList=[];
        fieldList.push({type:'string',value:'stateName',text:'状态名称',dictCode:''})
        fieldList.push({type:'string',value:'stateCode',text:'状态编码',dictCode:''})
        fieldList.push({type:'string',value:'code',text:'编号',dictCode:''})
        fieldList.push({type:'string',value:'reportUnitName',text:'上报单位',dictCode:''})
        fieldList.push({type:'int',value:'year',text:'上报年度',dictCode:''})
        fieldList.push({type:'string',value:'versionNo',text:'版本号',dictCode:''})
        fieldList.push({type:'int',value:'projectCount',text:'项目数',dictCode:''})
        fieldList.push({type:'string',value:'submitterId',text:'提交人',dictCode:''})
        fieldList.push({type:'string',value:'submitterName',text:'提交人Name',dictCode:''})
        fieldList.push({type:'string',value:'submitTime',text:'提交时间',dictCode:''})
        fieldList.push({type:'BigDecimal',value:'currentAmount',text:'本级投资总资金',dictCode:''})
        fieldList.push({type:'BigDecimal',value:'lowerAmount',text:'下级投资总资金',dictCode:''})
        fieldList.push({type:'BigDecimal',value:'totalAmount',text:'投资总资金',dictCode:''})
        fieldList.push({type:'string',value:'creatorId',text:'登记人ID',dictCode:''})
        fieldList.push({type:'string',value:'creatorName',text:'登记人Name',dictCode:''})
        fieldList.push({type:'string',value:'modifierId',text:'修改人ID',dictCode:''})
        fieldList.push({type:'string',value:'modifierName',text:'修改人Name',dictCode:''})
        this.superFieldList = fieldList
      }

    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>