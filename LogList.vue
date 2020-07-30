<template>
  <a-card :bordered="false"
          style="height: 100%">
    <!--导航区域-->
    <div>
      <a-tabs defaultActiveKey="1"
              @change="callback">
        <a-tab-pane tab="登录日志"
                    key="1"></a-tab-pane>
        <a-tab-pane tab="操作日志"
                    key="2"></a-tab-pane>
      </a-tabs>
    </div>

    <!-- 查询区域 -->
    <div class="table-page-search-wrapper"
         style="margin-bottom: 10px">
      <a-form layout="inline"
              @keyup.enter.native="searchQuery">
        <a-row :gutter="24">

          <a-col :md="6"
                 :sm="8">
            <a-form-item label="搜索日志">
              <a-input placeholder="请输入搜索关键词"
                       v-model="queryParam.keyWord"></a-input>
            </a-form-item>
          </a-col>

          <a-col :md="6"
                 :sm="8">
            <a-form-item label="操作用户">
              <a-input placeholder="请输入操作用户"
                       v-model="queryParam.username"></a-input>
            </a-form-item>
          </a-col>

          <a-col :md="6"
                 :sm="10">
            <a-form-item label="创建时间">
              <a-range-picker style="width: 210px"
                              v-model="queryParam.createTimeRange"
                              format="YYYY-MM-DD"
                              :placeholder="['开始时间', '结束时间']"
                              @change="onDateChange"
                              @ok="onDateOk" />
            </a-form-item>
          </a-col>
          <!-- <a-col :md="5" :sm="8" v-if="tabKey === '2'">
            <a-form-item label="操作类型" style="left: 10px">
              <j-dict-select-tag v-model="queryParam.operateType" placeholder="请选择操作类型" dictCode="operate_type"/>
            </a-form-item>
          </a-col>-->

          <span style="float: left;"
                class="table-page-search-submitButtons">
            <a-col>
              <a-button type="primary"
                        style="left: 10px"
                        @click="searchQuery"
                        icon="search">查询</a-button>
              <a-button type="primary"
                        @click="searchReset"
                        icon="reload"
                        style="margin-left: 8px;left: 10px">重置</a-button>
            </a-col>
          </span>

        </a-row>
      </a-form>
    </div>

    <!-- table区域-begin -->

    <a-table bordered
             ref="table"
             size="middle"
             rowKey="id"
             :columns="columns"
             :dataSource="dataSource"
             :pagination="ipagination"
             :loading="loading"
             @change="handleTableChange"
             :scroll="{ y: 550 }">

      <div v-show="queryParam.logType==2"
           slot="expandedRowRender"
           slot-scope="record"
           style="margin: 0">
        <div style="margin-bottom: 5px">
          <a-badge status="success"
                   style="vertical-align: middle;" /><span style="vertical-align: middle;">请求方法:{{ record.method }}</span></div>
        <div>
          <a-badge status="processing"
                   style="vertical-align: middle;" /><span style="vertical-align: middle;">请求参数:
            <div style="width:1520px;white-space:nowrap;text-overflow:ellipsis;overflow:hidden;">
              <j-ellipsis :value="record.requestParam"
                          :length="40" />
            </div>
          </span></div>
      </div>
      <!-- 字符串超长截取省略号显示-->
      <span slot="logContent"
            slot-scope="text, record">
        <j-ellipsis :value="text"
                    :length="40" />
      </span>
      <span slot="logType"
            slot-scope="text, record">
        <span v-if="record.logType=='2'">操作日志</span>
        <span v-if="record.logType=='1'">登录日志</span>
      </span>
      <span slot="operateType"
            slot-scope="text, record">
        <span v-if="record.operateType=='2'">添加</span>
        <span v-if="record.operateType=='1'">查询</span>
        <span v-if="record.operateType=='3'">修改</span>
        <span v-if="record.operateType=='4'">删除</span>
      </span>
    </a-table>
    <!-- 封装的公共表格组件 -->
    <publicTable :tableData="data1"
                 bordered
                 showHeader
                 tableLayout="fixed"
                 :options="optionsTable"
                 :rowSelection="rowSelection"
                 :total=100
                 :pageSizeOptions="['10','20','30']"
                 :pageSize=10
                 :defaultPageSize=10
                 :defaultCurrent=3
                 :showSizeChanger="true"
                 showQuickJumper
                 paginationSize='small'
                 @page="page"
                 @showSize="showSize"
                 :columns="columns1">
      <!-- <a slot="name"
         slot-scope="text">{{ text }}</a> -->
      <a slot="name"
         slot-scope="text,record">{{text}}</a>
    </publicTable>

    <!-- 封装的公共表单组件 -->
    <a-form :form="form"
            :label-col="{ span: 5 }"
            :wrapper-col="{ span: 12 }"
            @submit="handleSubmit">
      <public-form-item label="输入框"
                        labelAlign="right"
                        type="text"
                        required
                        show
                        allowClear
                        :v-decorator="[
          'email',
          {
              initialValue:'abc',
            rules: [
              {
                type: 'email',
                message: 'The input is not valid E-mail!',
              },
              {
                required: true,
                message: 'Please input your E-mail!',
              },
            ],
          },
        ]"
                        placeholder="请输入文本">

      </public-form-item>
      <public-form-item label="数字输入框"
                        type="number"
                        required
                        :v-decorator="[
          'num',{ initialValue: num }]"
                        placeholder="请输入数字">

      </public-form-item>
      <public-form-item label="密码输入框"
                        allowClear
                        labelAlign="left"
                        type="password"
                        :v-decorator="[
          'password',{ initialValue: '' }]"
                        placeholder="请输入密码">

      </public-form-item>
      <public-form-item label="文本域"
                        allowClear
                        type="textarea"
                        :v-decorator="[
          'textarea']"
                        placeholder="请输入描述信息">

      </public-form-item>
      <public-form-item label="单选按钮"
                        type="radio"
                        :v-decorator="[
          'radio']"
                        disabled
                        checked>

      </public-form-item>
      <public-form-item label="单选按钮组"
                        :options="options"
                        :v-decorator="[
          'radioGroup',{initialValue:'Apples'}]"
                        type="radioGroup">

      </public-form-item>
      <public-form-item label="多选按钮"
                        :v-decorator="[
          'checkbox']"
                        type="checkbox">

      </public-form-item>
      <public-form-item label="多选按钮组"
                        :v-decorator="[
          'checkboxGroup',{initialValue:['Apples']}]"
                        type="checkboxGroup"
                        :options="options">

      </public-form-item>
      <public-form-item label="下拉框"
                        allowClear
                        type="select"
                        :v-decorator="[
          'select',{initialValue:['Apples']}]"
                        :options="options"
                        showSearch
                        mode="multiple"
                        size="large">

      </public-form-item>

      <public-form-item label="时间选择器"
                        show
                        :v-decorator="[
          'timePicker',{initialValue: moment('11:13', 'HH:mm')}]"
                        placeholder="请选择时间"
                        type="timePicker">

      </public-form-item>

      <public-form-item label="日期选择器"
                        type="datePicker"
                        showToday
                        :v-decorator="[
          'datePicker',{initialValue: moment('2017-01-01')}]"
                        showTime
                        format="YYYY-MM-DD">

      </public-form-item>
      <public-form-item label="日期范围选择器"
                        :v-decorator="[
          'rangePicker',{initialValue: [moment('2016-08-01'), moment('2019-01-01')]}]"
                        format='YYYY/MM/DD'
                        type="rangePicker">

      </public-form-item>
      <public-form-item label="级联"
                        :v-decorator="[
          'cascader',{initialValue:  ['jiangsu', 'nanjing', 'zhonghuamen']}]"
                        type="cascader"
                        placeholder="请选择你好"
                        showSearch
                        expandTrigger="hover"
                        :options="options1">

      </public-form-item>
      <a-form-item>
        <a-button type="primary"
                  html-type="submit">
          Register
        </a-button>
      </a-form-item>

    </a-form>

    <!-- table区域-end -->
  </a-card>
</template>

<script>
import moment from 'moment'
import { getDataPage } from '@/api/list'
import JEllipsis from '@/components/inspect/JEllipsis'
import publicFormItem from '@/components/common/publicFormItem.vue'
import publicTable from '@/components/common/publicTable.vue'
export default {
  name: 'LogList',
  components: {
    JEllipsis,
    publicFormItem,
    publicTable
  },
  data () {
    return {
      // 封装的表格组件
      rowSelection: {
        columnWidth: '100px',
        columnTitle: '单选框',
        type: 'radio'

      },
      columns1: [
        {
          title: 'Name',
          dataIndex: 'name',
          key: 'name',
          scopedSlots: { customRender: 'name' }
        },
        {
          title: 'Age',
          dataIndex: 'age',
          key: 'age',
          width: 80
        },
        {
          title: 'Address',
          dataIndex: 'address',
          key: 'address 1',
          ellipsis: true
        },
        {
          title: 'operation',
          dataIndex: 'operation',
          scopedSlots: { customRender: 'operation' }
        }
      ],
      optionsTable: [
        {
          type: 'primary',
          title: '编辑',
          event: this.editTable,
          isShow: item => {
            return item.status !== 0
          }
        },
        {
          type: 'danger',
          title: '删除',
          event: this.deleteTable,
          isShow: item => {
            return item.status !== 1
          }
        }
      ],
      data1: [
        {
          key: '1',
          name: 'John Brown',
          age: 32,
          address: 'New York No. 1 Lake Park, New York No. 1 Lake Park',
          tags: ['nice', 'developer']
        },
        {
          key: '2',
          name: 'Jim Green',
          age: 42,
          address: 'London No. 2 Lake Park, London No. 2 Lake Park',
          tags: ['loser']
        },
        {
          key: '3',
          name: 'Joe Black',
          age: 32,
          address: 'Sidney No. 1 Lake Park, Sidney No. 1 Lake Park',
          tags: ['cool', 'teacher']
        }
      ],
      // 封装的表单的数据
      time: moment('11:08', 'HH:mm'),
      num: 4,
      cascader: ['jiangsu', 'nanjing', 'zhonghuamen'],
      rangePicker: [moment('2016-01-01'), moment('2019-01-01')],
      form: this.$form.createForm(this, { name: 'register' }),
      options1: [
        {
          value: 'zhejiang',
          label: 'Zhejiang',
          children: [
            {
              value: 'hangzhou',
              label: 'Hangzhou',
              children: [
                {
                  value: 'xihu',
                  label: 'West Lake'
                }
              ]
            }
          ]
        },
        {
          value: 'jiangsu',
          label: 'Jiangsu',
          children: [
            {
              value: 'nanjing',
              label: 'Nanjing',
              children: [
                {
                  value: 'zhonghuamen',
                  label: 'Zhong Hua Men'
                }
              ]
            }
          ]
        }
      ],
      options: ['Apples', 'Nails', 'Bananas', 'Helicopters'],
      //   本页面的数据
      description: '这是日志管理页面',
      // 查询条件
      queryParam: {
        ipInfo: '',
        createTimeRange: [],
        logType: '1',
        keyWord: '',
        username: ''
      },
      /* table加载状态 */
      loading: false,
      /* 分页参数 */
      ipagination: {
        current: 1,
        pageSize: 15,
        pageSizeOptions: ['15', '25', '35'],
        showTotal: (total, range) => {
          return range[0] + '-' + range[1] + ' 共' + total + '条'
        },
        showQuickJumper: true,
        showSizeChanger: true,
        total: 0
      },
      /* 排序参数 */
      isorter: {
        column: 'createTime',
        order: 'desc'
      },
      tabKey: '1',
      dataSource: [],
      // 表头
      columns: [
        {
          title: '#',
          dataIndex: '',
          key: 'rowIndex',
          width: 100,
          align: 'center',
          customRender: function (t, r, index) {
            return parseInt(index) + 1
          }
        },
        {
          title: '日志内容',
          dataIndex: 'logContent',
          width: 180,
          scopedSlots: { customRender: 'logContent' },
          sorter: true
        },
        {
          title: '操作人名称',
          width: 150,
          dataIndex: 'username',
          sorter: true
        },
        {
          title: 'IP',
          dataIndex: 'ip',
          width: 150,
          sorter: true
        },
        {
          title: '来源',
          dataIndex: 'loginClient',
          width: 100,
          sorter: true
        },
        {
          title: '耗时(毫秒)',
          dataIndex: 'costTime',
          width: 100,
          align: 'center',
          sorter: true
        },
        {
          title: '日志类型',
          dataIndex: 'logType',
          width: 150,
          scopedSlots: { customRender: 'logType' },
          /* customRender:function (text) {
                if(text==1){
                  return "登录日志";
                }else if(text==2){
                  return "操作日志";
                }else{
                  return text;
                }
              }, */
          align: 'center'
        },
        {
          title: '创建时间',
          dataIndex: 'createTime',
          width: 180,
          align: 'center',
          sorter: true
        }
      ],
      menuNameColumn: {
        title: '访问菜单',
        width: 120,
        align: 'center',
        dataIndex: 'menuName',
        sorter: true
      },
      operateColumn:
      {
        title: '操作类型',
        width: 120,
        dataIndex: 'operateType',
        align: 'center',
        scopedSlots: { customRender: 'operateType' }
      },
      labelCol: {
        xs: { span: 1 },
        sm: { span: 2 }
      },
      wrapperCol: {
        xs: { span: 10 },
        sm: { span: 16 }
      }
    }
  },
  methods: {
    //   封装表格的方法
    editTable (text, record) {
      console.log(record)
    },
    page (val) {
      console.log(val)
    },
    showSize (val) {
      console.log(val)
    },
    // 封装表单的方法
    moment,
    handleSubmit (e) {
      e.preventDefault()
      this.form.validateFields((err, values) => {
        if (!err) {
          console.log('Received values of form: ', values)
        }
      })
    },
    // 本页面的方法
    loadData (arg) {
      // 加载数据 若传入参数1则加载第一页的内容
      if (arg === 1) {
        this.ipagination.current = 1
      }
      var params = {
        page: this.ipagination.current,
        limit: this.ipagination.pageSize,
        sortname: this.isorter.column,
        sortorder: this.isorter.order
      }
      this.loading = true
      var url = '/system/public/sysLogApi/queryLoginPage'
      if (this.tabKey === '1') {
        url = '/system/public/sysLogApi/queryLoginPage'
      } else if (this.tabKey === '2') {
        url = '/system/public/sysLogApi/querySysLogPage'
      }

      var data = {
        logContent: this.queryParam.keyWord,
        username: this.queryParam.username,
        logType: this.queryParam.logType,
        createTimeBegin: this.queryParam.createTime_begin ? moment(this.queryParam.createTime_begin).format('YYYY-MM-DD HH:mm:ss') : '',
        createTimeEnd: this.queryParam.createTime_end ? (moment(this.queryParam.createTime_end).format('YYYY-MM-DD') + ' 23:59:59') : ''
      }
      var that = this

      getDataPage({ url, data, params }).then((res) => {
        var data = res.data
        debugger
        that.dataSource = data.rows
        that.ipagination.total = data.total
        that.ipagination.current = data.page
        that.ipagination.pageSize = data.rp
        that.isorter.column = data.sortName
        this.isorter.order = data.sortOrder
        that.loading = false
      })
    },
    searchQuery () {
      this.loadData(1)
    },
    searchReset () {
      this.queryParam = {}
      this.loadData(1)
    },
    handleTableChange (pagination, filters, sorter) {
      // 分页、排序、筛选变化时触发
      // TODO 筛选
      if (Object.keys(sorter).length > 0) {
        this.isorter.column = sorter.field
        this.isorter.order = sorter.order == 'ascend' ? 'asc' : 'desc'
      }
      this.ipagination = pagination
      this.loadData()
    },

    // 日志类型
    callback (key) {
      // 动态添加操作类型列

      if (key == 2) {
        this.tabKey = '2'
        this.columns.splice(1, 0, this.menuNameColumn)
        this.columns.splice(7, 0, this.operateColumn)
      } else if (this.columns.length === 10) {
        this.tabKey = '1'
        this.columns.splice(1, 1)
        this.columns.splice(6, 1)
      }

      let that = this
      that.queryParam.logType = key
      that.loadData()
    },
    onDateChange: function (value, dateString) {
      console.log(dateString[0], dateString[1])
      this.queryParam.createTime_begin = dateString[0]
      this.queryParam.createTime_end = dateString[1]
    },
    onDateOk (value) {
      console.log(value)
    }
  },
  mounted () {
    this.loadData()
  }
}
</script>

<style scoped>
@import "../../../../assets/less/common.less";
</style>
