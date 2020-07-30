<template>
  <!-- 已解决获取表单数据，校验 -->
  <a-form-item :label="label"
               :labelAlign="labelAlign"
               :labelCol="labelCol"
               :required="required">
    <!--12种 类型，设置初始值 -->
    <a-input v-if="type=='text'"
             :class="show && 'public-showInput'"
             v-bind="$attrs"
             v-on="$listeners"
             :disabled='disabled || show'
             :allowClear="allowClear"
             :size="size"
             v-decorator="vDecorator"
             :placeholder="placeholder" />

    <a-input-number v-if="type=='number'"
                    :class="show && 'public-showInput'"
                    v-bind="$attrs"
                    v-on="$listeners"
                    :disabled="disabled || show"
                    :allowClear="allowClear"
                    :size="size"
                    v-decorator="vDecorator"
                    :placeholder="placeholder" />

    <a-input-password v-if="type=='password'"
                      :class="show && 'public-showInput'"
                      v-bind="$attrs"
                      v-on="$listeners"
                      :disabled="disabled || show"
                      v-decorator="vDecorator"
                      :allowClear="allowClear"
                      :size="size"
                      :placeholder="placeholder" />

    <a-textarea v-if="type=='textarea'"
                :class="show && 'public-showInput'"
                v-bind="$attrs"
                v-on="$listeners"
                :disabled="disabled || show"
                :allowClear="allowClear"
                v-decorator="vDecorator"
                :size="size"
                :placeholder="placeholder" />

    <a-radio v-if="type=='radio'"
             v-bind="$attrs"
             v-on="$listeners"
             :disabled="disabled"
             v-decorator="vDecorator"
             :size="size"
             :checked="checked"></a-radio>

    <a-radio-group v-if="type=='radioGroup'"
                   :options="options"
                   v-decorator="vDecorator"
                   :disabled="disabled"
                   :size="size" />

    <a-checkbox v-if="type=='checkbox'"
                v-bind="$attrs"
                v-on="$listeners"
                :size="size"
                v-decorator="vDecorator"
                :disabled="disabled"
                :checked="checked">
    </a-checkbox>

    <a-checkbox-group v-if="type=='checkboxGroup'"
                      :disabled="disabled"
                      :size="size"
                      v-decorator="vDecorator"
                      :options="options" />

    <a-select v-if="type=='select'"
              :class="show && 'public-showInput'"
              :placeholder="placeholder"
              :disabled="disabled || show"
              v-decorator="vDecorator"
              :size="size"
              :showArrow="showArrow"
              :allowClear="allowClear"
              :show-search="showSearch"
              :mode="mode">
      <a-select-option v-for="(item,index) in options"
                       :key="index"
                       :value="item">
        {{ item }}
      </a-select-option>

    </a-select>

    <!-- 初始值，搜索功能 -->
    <a-time-picker v-if="type=='timePicker'"
                   :class="show && 'public-showInput'"
                   v-bind="$attrs"
                   v-decorator="vDecorator"
                   v-on="$listeners"
                   :placeholder="placeholder"
                   :disabled="disabled || show"
                   :size="size"
                   :use12Hours="use12Hours"
                   :format="format" />

    <a-date-picker v-if="type=='datePicker'"
                   :class="show && 'public-showInput'"
                   v-bind="$attrs"
                   :placeholder="placeholder"
                   :size="size"
                   v-decorator="vDecorator"
                   v-on="$listeners"
                   :format="format"
                   :showTime='showTime'
                   :showToday="showToday"
                   :disabled="disabled || show" />

    <a-range-picker v-if="type=='rangePicker'"
                    :class="show && 'public-showInput'"
                    v-bind="$attrs"
                    :size="size"
                    :format="format"
                    v-decorator="vDecorator"
                    v-on="$listeners"
                    :disabled="disabled || show" />

    <a-cascader v-if="type=='cascader'"
                :class="show && 'public-showInput'"
                v-bind="$attrs"
                v-on="$listeners"
                :size="size"
                :options="options"
                v-decorator="vDecorator"
                :disabled="disabled || show"
                :showSearch="showSearch"
                :expandTrigger="expandTrigger"
                :placeholder="placeholder" />
  </a-form-item>
</template>

<script>
import moment from 'moment'
export default {
  name: 'public-form-item',
  props: {
    label: {
      type: String,
      required: true,
      default: 'label'
    },
    labelCol: {
      type: Object
    },
    labelAlign: {
      type: String,
      default: 'right'
    },
    required: {
      type: Boolean,
      default: false
    },
    vDecorator: {
      type: Array
    },
    type: {
      type: String,
      default: 'text'
    },
    value: {
      type: [String, Object, Array, Number]
    },
    disabled: {
      type: Boolean,
      default: false
    },
    checked: {
      type: Boolean,
      default: false
    },
    allowClear: {
      type: Boolean,
      default: false
    },
    size: {
      type: String,
      default: 'default'
    },
    placeholder: {
      type: [String, Array],
      default: ''
    },
    mode: {
      type: String,
      default: ''
    },
    showArrow: {
      type: Boolean,
      default: true
    },
    options: {
      type: Array
    },
    showSearch: {
      type: Boolean,
      default: false
    },
    defaultValue: {
      type: [String, Number]
    },
    initialValue: {
      type: [String, Number]
    },
    format: {
      type: String,
      default: 'HH:mm:ss'
    },
    use12Hours: {
      type: Boolean,
      default: false
    },
    defaultOpenValue: {
      type: moment
    },
    showToday: {
      type: Boolean,
      default: true
    },
    showTime: {
      type: [Boolean, Object],
      default: false
    },
    options: {
      type: [Array, Object]
    },
    expandTrigger: {
      type: String,
      default: 'click'
    },
    show: {
      type: Boolean,
      default: false
    }

  },
  methods: {
    moment
  }
}
</script>

<style>
.ant-input-number {
  width: 100% !important;
}
.ant-time-picker {
  width: 100% !important;
}

.ant-calendar-picker {
  width: 100% !important;
}

/*form表单-展示型不加border*/
.public-showInput > input,
.public-showInput > div > input,
.public-showInput > textarea {
  border: 0;
  resize: none;
}
</style>
