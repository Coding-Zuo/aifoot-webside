<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <div v-if="crud.props.searchToggle">
        <!-- 搜索 -->
        <el-input v-model="query.value" clearable placeholder="输入搜索内容" style="width: 200px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <el-select v-model="query.type" clearable placeholder="类型" class="filter-item" style="width: 130px">
          <el-option v-for="item in queryTypeOptions" :key="item.key" :label="item.display_name" :value="item.key" />
        </el-select>
        <rrOperation :crud="crud" />
      </div>
      <!--如果想在工具栏加入更多按钮，可以使用插槽方式， slot = 'left' or 'right'-->
      <crudOperation :permission="permission" />
      <!--表单组件-->
      <el-dialog :close-on-click-modal="false" :before-close="crud.cancelCU" :visible.sync="crud.status.cu > 0" :title="crud.status.title" width="500px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="80px">
          <el-form-item label="微信openid" prop="openId">
            <el-input v-model="form.openId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="昵称">
            <el-input v-model="form.nickname" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="头像">
            <el-input v-model="form.nickpic" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="性别">
                未设置字典，请手动设置 Radio
          </el-form-item>
          <el-form-item label="电话号">
            <el-input v-model="form.phone" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="语言">
            <el-input v-model="form.language" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="头像">
            <el-input v-model="form.headimgurl" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="国家">
            <el-input v-model="form.country" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="省份">
            <el-input v-model="form.province" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="城市">
            <el-input v-model="form.city" style="width: 370px;" />
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button type="text" @click="crud.cancelCU">取消</el-button>
          <el-button :loading="crud.cu === 2" type="primary" @click="crud.submitCU">确认</el-button>
        </div>
      </el-dialog>
      <!--表格渲染-->
      <el-table ref="table" v-loading="crud.loading" :data="crud.data" size="small" style="width: 100%;" @selection-change="crud.selectionChangeHandler">
        <el-table-column type="selection" width="55" />
        <el-table-column prop="id" label="id" />
        <el-table-column prop="openId" label="微信openid" />
        <el-table-column prop="nickname" label="昵称" />
        <el-table-column prop="nickpic" label="头像" />
        <el-table-column prop="sex" label="性别" />
        <el-table-column prop="phone" label="电话号" />
        <el-table-column prop="language" label="语言" />
        <el-table-column prop="headimgurl" label="头像" />
        <el-table-column prop="country" label="国家" />
        <el-table-column prop="province" label="省份" />
        <el-table-column prop="city" label="城市" />
        <el-table-column v-permission="['admin','cpUser:edit','cpUser:del']" label="操作" width="150px" align="center">
          <template slot-scope="scope">
            <udOperation
              :data="scope.row"
              :permission="permission"
            />
          </template>
        </el-table-column>
      </el-table>
      <!--分页组件-->
      <pagination />
    </div>
  </div>
</template>

<script>
import crudCpUser from '@/api/cpUser'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'

const defaultForm = { id: null, openId: null, nickname: null, nickpic: null, sex: null, phone: null, language: null, headimgurl: null, country: null, province: null, city: null }
export default {
  name: 'CpUser',
  components: { pagination, crudOperation, rrOperation, udOperation },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  cruds() {
    return CRUD({ title: '测试生成', url: 'api/cpUser', sort: 'id,desc', crudMethod: { ...crudCpUser }})
  },
  data() {
    return {
      permission: {
        add: ['admin', 'cpUser:add'],
        edit: ['admin', 'cpUser:edit'],
        del: ['admin', 'cpUser:del']
      },
      rules: {
        id: [
          { required: true, message: 'id不能为空', trigger: 'blur' }
        ],
        openId: [
          { required: true, message: '微信openid不能为空', trigger: 'blur' }
        ]
      },
      queryTypeOptions: [
        { key: 'openId', display_name: '微信openid' },
        { key: 'nickname', display_name: '昵称' }
      ]
    }
  },
  methods: {
    // 获取数据前设置好接口地址
    [CRUD.HOOK.beforeRefresh]() {
      const query = this.query
      if (query.type && query.value) {
        this.crud.params[query.type] = query.value
      }
      return true
    }
  }
}
</script>

<style scoped>

</style>
