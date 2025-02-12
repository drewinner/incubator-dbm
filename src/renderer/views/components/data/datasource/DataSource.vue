<template>
  <el-dialog :title="title" :visible.sync="elementLoading.body" :width="width" @close="closeDialog">
    <el-form :model="form" size="mini">
      <el-card class="box-card" shadow="never" style="margin-top: -10px;">
        <div slot="header" class="clearfix" style="padding: -1px;">
          <span>{{ this.$t('common.setting_connection') }}</span>
        </div>
        <el-form-item :label="this.$t('common.alias_name')" :label-width="formLabelWidth">
          <el-tooltip placement="top">
            <div slot="content">
              <span v-html="this.$t('view.component.data.source.tooltip.alias_name')"></span>
            </div>
            <el-input
              :placeholder="this.$t('view.component.data.source.placeholder.alias_name')"
              v-model="form.name">
              <i slot="prefix" class="fa fa-tag"></i>
            </el-input>
          </el-tooltip>
        </el-form-item>
        <el-form-item :label="this.$t('common.host')" :label-width="formLabelWidth">
          <el-tooltip placement="top">
            <div slot="content">
              <span v-html="this.$t('view.component.data.source.tooltip.host')"></span>
            </div>
            <el-input
              :placeholder="this.$t('view.component.data.source.placeholder.host')"
              v-model="form.host">
              <i slot="prefix" class="fa fa-server"></i>
            </el-input>
          </el-tooltip>
        </el-form-item>
        <el-form-item :label="this.$t('common.port')" :label-width="formLabelWidth">
          <el-tooltip placement="top">
            <div slot="content">
              <span v-html="this.$t('view.component.data.source.tooltip.port')"></span>
            </div>
            <el-input
              :placeholder="this.$t('view.component.data.source.placeholder.port')"
              v-model="form.port">
              <i slot="prefix" class="fa fa-recycle"></i>
            </el-input>
          </el-tooltip>
        </el-form-item>
      </el-card>
      <el-card class="box-card" shadow="never">
        <div slot="header" class="clearfix" style="padding: -1px;">
          <span>{{ this.$t('common.advanced_connection') }}</span>
          <span><el-tag size="mini">{{ this.$t('common.beta') }}</el-tag></span>
        </div>
        <el-form-item>
          <el-switch v-model="form.delivery"></el-switch>
          <span v-html="this.$t('view.component.data.source.title.warning_drop')"></span>
        </el-form-item>
      </el-card>
    </el-form>
    <div slot="footer" class="dialog-footer">
      <el-button size="mini" :loading="elementLoading.test" type="success" @click="hadnlerTest()">{{ this.$t('common.test_connection') }}</el-button>
      <el-button size="mini" @click="elementLoading.body = false">{{ this.$t('common.cancel') }}</el-button>
      <el-button type="primary" size="mini" @click="handlerProcessor()">{{ this.$t('common.ok') }}</el-button>
    </div>
  </el-dialog>
</template>

<script>
import { saveDataSource, getConnection } from '@/services/DataSource'

export default {
  name: 'DataSource',
  props: {
    title: {
      type: String,
      default: ''
    },
    loading: {
      type: Boolean,
      default: false
    },
    width: {
      type: String,
      default: '50%'
    }
  },
  created() {
  },
  data() {
    return {
      form: {
        name: '',
        host: '',
        port: '',
        userName: '',
        password: '',
        delivery: false
      },
      formLabelWidth: '100px',
      elementLoading: {
        body: false,
        test: false
      }
    }
  },
  methods: {
    async handlerProcessor() {
      const response = await saveDataSource(this.form)
      if (!response.status) {
        this.$notify.error({
          title: 'Error',
          message: response.message
        })
      } else {
        this.$notify.success({
          title: 'Success',
          message: response.message
        })
        this.closeDialog()
        this.$emit('refresh')
      }
    },
    async hadnlerTest() {
      this.elementLoading.test = true
      const response = await getConnection(this.form.host, this.form.port)
      if (!response.status) {
        this.$notify.error({
          title: 'Error',
          message: response.message
        })
      } else {
        this.$notify.success({
          title: 'Success',
          message: response.message
        })
      }
      this.elementLoading.test = false
    },
    closeDialog() {
      this.$emit('close')
    }
  },
  watch: {
    loading: {
      deep: true,
      handler() {
        this.elementLoading.body = this.loading
      }
    },
    width: {
      deep: true
    },
    title: {
      deep: true
    }
  }
}
</script>

<style scoped>
  /deep/ .el-card__header {
    padding: 5px 15px;
  }
</style>
