<template>
  <div>
    <a-table :columns="columns"
             v-bind="$attrs"
             v-on="$listeners"
             :pagination="false"
             :loading="loading"
             :size="size"
             :indentSize="indentSize"
             :showHeader="showHeader"
             :rowSelection="rowSelection"
             :bordered="bordered"
             :scroll="scroll"
             :defaultExpandedRowKeys="defaultExpandedRowKeys"
             :defaultExpandAllRows="defaultExpandAllRows"
             :expandRowByClick="expandRowByClick"
             :rowKey="rowKey"
             :rowClassName="rowClassName"
             :tableLayout="tableLayout"
             :data-source="tableData">

      <template slot="operation"
                slot-scope="text, record">
        <template v-for="(btn,index) in options">
          <a-button :type="btn.type"
                    :key="index"
                    @click="btn.event(text,record)">{{btn.title}}</a-button>
        </template>
      </template>

    </a-table>
    <a-pagination :pageSizeOptions="pageSizeOptions"
                  :total="total"
                  :showTotal="total => `共 ${total} 条`"
                  :pageSize="pageSize"
                  :simple="simple"
                  :hideOnSinglePage="hideOnSinglePage"
                  :showSizeChanger="showSizeChanger"
                  :defaultPageSize="defaultPageSize"
                  :defaultCurrent="defaultCurrent"
                  :showQuickJumper="showQuickJumper"
                  :size="paginationSize"
                  v-bind="$attrs"
                  @change="change"
                  @showSizeChange="onShowSizeChange"
                  v-on="$listeners">
    </a-pagination>
  </div>
</template>

<script>
export default {
  name: 'public-table',
  props: {
    columns: {
      type: Array,
      required: true
    },
    tableData: {
      type: Array,
      default: function () {
        return []
      }
    },
    loading: {
      type: Boolean,
      default: false
    },
    size: {
      type: String,
      default: 'default'
    },
    bordered: {
      type: Boolean,
      default: false
    },
    showHeader: {
      type: Boolean,
      default: false
    },
    rowSelection: {
      type: Object,
      default: null
    },
    scroll: {
      type: Object
    },
    indentSize: {
      type: Number,
      default: 15
    },
    defaultExpandAllRows: {
      type: Boolean,
      default: false
    },
    defaultExpandedRowKeys: {
      type: String
    },
    expandRowByClick: {
      type: Boolean,
      default: false
    },
    rowKey: {
      type: String
    },
    rowClassName: {
      type: Function,
      default () {
        return ''
      }
    },
    tableLayout: {
      type: String
    },
    options: {
      type: Array
    },
    pageSizeOptions: {
      type: Array
    },
    total: {
      type: Number,
      default: 0
    },
    pageSize: {
      type: Number,
      default: 10
    },
    showQuickJumper: {
      type: Boolean,
      default: false
    },
    simple: {
      type: Boolean,
      default: false
    },
    hideOnSinglePage: {
      type: Boolean,
      default: false
    },
    showSizeChanger: {
      type: Boolean,
      default: false
    },
    defaultPageSize: {
      type: Number,
      default: 10
    },
    defaultCurrent: {
      type: Number,
      default: 1
    },
    paginationSize: {
      type: String
    }
  },
  methods: {
    change (val) {
      this.$emit('page', val)
    },
    onShowSizeChange (current, size) {
      this.$emit('showSize', [current, size])
    }
  }
}
</script>

<style>
.ant-pagination {
  text-align: right;
  margin: 16px 0;
}
</style>
