<template>
  <div class="FileCatetoryList">
    <div class="tree">
      <a-tree @select="handleSelect" :tree-data="treeDataPath" :load-data="onLoadData"></a-tree>
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
        <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>
        <a-button type="primary" icon="download" @click="handleExportXls('文件归档中间表')">导出</a-button>
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
          :scroll="{ x: true }"
          bordered
          rowKey="id"
          :columns="columns"
          :dataSource="dataSource"
          :pagination="ipagination"
          :loading="loading"
          :rowSelection="{ selectedRowKeys: selectedRowKeys, onChange: onSelectChange }"
          class="j-table-force-nowrap"
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
          <template slot="fileSlot" slot-scope="text">
            <span v-if="!text" style="font-size: 12px; font-style: italic">无文件</span>
            <a-button v-else :ghost="true" type="primary" icon="download" size="small" @click="downloadFile(text)">
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
                  <a @click="handleDetail(record)">详情</a>
                </a-menu-item>
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

      <file-catetory-modal ref="modalForm" @ok="modalFormOk"></file-catetory-modal>
    </a-card>
  </div>
</template>

<script>
import { axios } from '@/utils/request'

import '@/assets/less/TableExpand.less'
import { mixinDevice } from '@/utils/mixin'
import { JeecgListMixin } from '@/mixins/JeecgListMixin'
import FileCatetoryModal from './modules/FileCatetoryModal'
import { loadCategoryData } from '@/api/api'
import { filterMultiDictText } from '@/components/dict/JDictSelectUtil'

export default {
  name: 'FileCatetoryList',
  mixins: [JeecgListMixin, mixinDevice],
  components: {
    FileCatetoryModal,
  },
  data() {
    return {
      treeDataPath: [],
      description: '文件归档中间表管理页面',
      // 表头
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
          title: '文件名称',
          align: 'center',
          dataIndex: 'fileName',
        },
        {
          title: '文件路径',
          align: 'center',
          dataIndex: 'filePath',
          scopedSlots: { customRender: 'fileSlot' },
        },
        {
          title: '归档路径id',
          align: 'center',
          dataIndex: 'cateId',
          customRender: (text) => (text ? filterMultiDictText(this.dictOptions['cateId'], text) : ''),
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
        tree: '/sys/category/loadTreeData',
        list: '/catatory/fileCatetory/list',
        delete: '/catatory/fileCatetory/delete',
        deleteBatch: '/catatory/fileCatetory/deleteBatch',
        exportXlsUrl: '/catatory/fileCatetory/exportXls',
        importExcelUrl: 'catatory/fileCatetory/importExcel',
      },
      dictOptions: {},
      superFieldList: [],
    }
  },
  created() {
    this.getSuperFieldList()
    this.getPathTree()
  },
  computed: {
    importExcelUrl: function () {
      return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`
    },
  },
  methods: {
    async handleSelect(id) {
      console.log(id[0])

      let res = await axios.get(this.url.list, { params: { cateId: id[0] } })
      this.dataSource = res.result.records
    },
    onLoadData(treeNode) {
      return new Promise(async (resolve) => {
        if (treeNode.dataRef.children) {
          resolve()
          return
        }
        let res = await axios.get(this.url.tree, { params: { pcode: 'B03', pid: treeNode.dataRef.key } })
        console.log(treeNode)
        treeNode.dataRef.children = res.result
        this.treeDataPath = [...this.treeDataPath]
        resolve()
      })
    },
    async getPathTree() {
      let result = await axios.get(this.url.tree, { params: { pcode: 'B03' } })
      this.treeDataPath = result.result
    },
    initDictConfig() {
      loadCategoryData({ code: 'B03' }).then((res) => {
        if (res.success) {
          this.$set(this.dictOptions, 'cateId', res.result)
        }
      })
    },
    getSuperFieldList() {
      let fieldList = []
      fieldList.push({ type: 'string', value: 'fileName', text: '文件名称', dictCode: '' })
      fieldList.push({ type: 'string', value: 'filePath', text: '文件路径', dictCode: '' })
      fieldList.push({ type: 'string', value: 'cateId', text: '归档路径id' })
      this.superFieldList = fieldList
    },
  },
}
</script>
<style scoped lang="less">
@import '~@assets/less/common.less';
.FileCatetoryList {
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