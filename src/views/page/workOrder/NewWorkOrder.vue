<template>
  <div class="new-work-order">
    <h1>新建工单</h1>
    <!-- 添加标题 -->
    <el-form ref="formRef" :model="form" :rules="rules" label-width="150px" class="work-order-form">
      <!-- 工单名称与描述 -->
      <el-form-item label="工单名称" prop="name" required :show-message="false">
        <el-input v-model="form.name" placeholder="请输入工单名称" clearable></el-input>
      </el-form-item>

      <el-form-item label="描述">
        <el-input
          v-model="form.description"
          type="textarea"
          placeholder="请输入描述"
          style="width: 500px"
          clearable
        ></el-input>
      </el-form-item>

      <el-form-item label="是否停机" required>
        <el-switch
          v-model="form.halt_type"
          :active-value="1"
          :inactive-value="0"
          inline-prompt
          active-text="是"
          inactive-text="否"
          style="--el-switch-on-color: #f65650; width: 70px"
        />
      </el-form-item>

      <!-- 目标设备选择 -->
      <div class="equipment">
        <el-form-item label="涉及资产" required>
          <el-row gutter="5">
            <el-col :span="20">
              <el-form-item label="" class="form-item" prop="production_line_id" :show-message="false" required>
                <el-select
                  class="equipment-fields"
                  v-model="form.production_line_id"
                  placeholder="生产线 (层级 1)"
                  clearable
                >
                  <el-option
                    v-for="item in commonDataStore.productionLines"
                    :key="item.id"
                    :label="item.name"
                    :value="item.id"
                  />
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="20">
              <el-form-item label="" class="form-item">
                <el-select
                  class="equipment-fields"
                  v-model="form.equipment_group_id"
                  placeholder="设备组 (层级 2)"
                  clearable
                >
                  <el-option v-for="item in equipmentGroups" :key="item.id" :label="item.name" :value="item.id" />
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="20">
              <el-form-item label="" class="form-item">
                <el-select class="equipment-fields" v-model="form.equipment_id" placeholder="设备 (层级 3)" clearable>
                  <el-option v-for="item in equipments" :key="item.id" :label="item.name" :value="item.id" />
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="20">
              <el-form-item label="" class="form-item">
                <el-select class="equipment-fields" v-model="form.component_id" placeholder="组件 (层级 4)" clearable>
                  <el-option v-for="item in components" :key="item.id" :label="item.name" :value="item.id" />
                </el-select>
              </el-form-item>
            </el-col>
          </el-row>
        </el-form-item>
      </div>

      <div class="categorization">
        <el-form-item label="具体分类" required>
          <!-- 新增三个下拉菜单 -->
          <el-col :span="20">
            <el-form-item label="" class="form-item" placeholder="优先级" prop="priority_id" :show-message="false">
              <el-select v-model="form.priority_id" placeholder="优先级" clearable>
                <el-option
                  v-for="item in commonDataStore.priorities"
                  :key="item.id"
                  :label="item.name"
                  :value="item.id"
                />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="20">
            <el-form-item label="" class="form-item" prop="category_id" :show-message="false">
              <el-select v-model="form.category_id" placeholder="工单类别" clearable>
                <el-option
                  v-for="item in commonDataStore.categories"
                  :key="item.id"
                  :label="item.name"
                  :value="item.id"
                />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="20">
            <el-form-item label="" class="form-item" prop="work_type_id" :show-message="false">
              <el-select v-model="form.work_type_id" placeholder="工作类型" clearable>
                <el-option
                  v-for="item in commonDataStore.workTypes"
                  :key="item.id"
                  :label="item.name"
                  :value="item.id"
                />
              </el-select>
            </el-form-item>
          </el-col>
        </el-form-item>
      </div>
      <RecurrenceEditor v-model:recurrenceSetting="form.recurrence_setting" />
      <UploadEditor v-model:imageList="form.image_list" v-model:filesList="form.files_list" />
      <!-- 提交按钮 -->
      <el-form-item>
        <el-button type="primary" @click="submitForm">创建工单</el-button>
      </el-form-item>

      <!--      <el-form-item>-->
      <!--        <el-button type="primary" @click="uploadFilesToServer">上传到服务器</el-button>-->
      <!--      </el-form-item>-->
    </el-form>
  </div>
</template>

<script>
import RecurrenceEditor from '@/components/Calendar/recurrenceEditor.vue'
import UploadEditor from '@/components/Others/upload.vue'
import { uploadMultipleToMinio } from '@/api/minio'
import { useTagsViewStore } from '@/store'
import { useCommonDataStore } from '@/store/modules/commonData'

import { getEquipmentGroups, getEquipments, getEquipmentComponents } from '@/api/equipment'

// import {
//   getEquipmentGroups
// } from '@/api/PostgREST'

import { getTimeZone } from '@/utils/datetime'
import { createWorkOrder } from '@/api/workOrder'

export default {
  components: { RecurrenceEditor, UploadEditor },
  setup() {
    const commonDataStore = useCommonDataStore()

    // 加载数据 (只需在页面加载时调用一次)
    commonDataStore.fetchPriorities()
    commonDataStore.fetchWorkTypes()
    commonDataStore.fetchCategories()
    commonDataStore.fetchProductionLines()

    return {
      commonDataStore,
    }
  },
  data() {
    return {
      widthControl: '500px',
      form: {
        name: '',
        description: '',
        estimated_minutes: 30,
        production_line_id: null,
        equipment_group_id: null,
        equipment_id: null,
        component_id: null,
        priority_id: null,
        category_id: null,
        work_type_id: null,
        state_id: 1,
        halt_type: 0,
        time_zone: getTimeZone(),
        created_by: 37,
        recurrence_type: null,
        image_list: [],
        files_list: [],
        recurrence_setting: {},
      },
      equipmentGroups: [],
      equipments: [],
      components: [],
      rules: {
        name: [{ required: true, message: '请输入工单名称', trigger: 'blur' }],
        halt_type: [{ required: true, message: '请选择是否停机', trigger: 'change' }],
        production_line_id: [{ required: true, message: '请选择生产线', trigger: 'change' }],
        priority_id: [{ required: true, message: '请选择优先级', trigger: 'change' }],
        category_id: [{ required: true, message: '请选择工单类别', trigger: 'change' }],
        work_type_id: [{ required: true, message: '请选择工作类型', trigger: 'change' }],
        recurrence_type: [{ required: true, message: '请选择重复设置', trigger: 'change' }],
        'recurrence_setting.start_date_time': [{ required: true, message: '请选择开始时间', trigger: 'change' }],
        'recurrence_setting.end_date_time': [{ required: true, message: '请选择结束时间', trigger: 'change' }],
      },
    }
  },
  mounted() {},
  methods: {
    async fetchEquipmentGroups() {
      const { data } = await getEquipmentGroups(this.form.production_line_id)
      this.equipmentGroups = data.data
    },

    // use PostgREST to fetch equipment groups
    // async fetchEquipmentGroups() {
    //   const { data } = await getEquipmentGroups( this.form.production_line_id )
    //   this.equipmentGroups = data
    // },

    async fetchEquipments() {
      const { data } = await getEquipments(this.form.equipment_group_id)
      this.equipments = data.data
    },

    async fetchComponents() {
      const { data } = await getEquipmentComponents(this.form.equipment_id)
      this.components = data.data
    },

    async uploadFilesToServer() {
      try {
        let uploadedImages = []
        let uploadedFiles = []

        // 如果存在图片才上传
        if (this.form.image_list.length > 0) {
          const imageRes = await uploadMultipleToMinio(this.form.image_list)
          uploadedImages = imageRes.data.uploadedFiles || []
          console.log('🖼 上传图片成功:', uploadedImages)
          this.form.image_list = uploadedImages.map(file => file.url)
        }

        // 如果存在文件才上传
        if (this.form.files_list.length > 0) {
          const fileRes = await uploadMultipleToMinio(this.form.files_list)
          uploadedFiles = fileRes.data.uploadedFiles || []
          console.log('📄 上传文件成功:', uploadedFiles)
          this.form.files_list = uploadedFiles.map(file => file.url)
        }

        console.log('✅ 当前上传后的图片列表:', this.form.image_list)
        console.log('✅ 当前上传后的文件列表:', this.form.files_list)
        this.$message.success('文件上传成功！')
      } catch (err) {
        console.error('❌ 文件上传失败:', err)
        this.$message.error('文件上传失败')
      }
    },

    async submitForm() {
      this.$refs.formRef.validate(async valid => {
        if (!valid) return

        // 先上传图片和文件
        await this.uploadFilesToServer()

        const payload = {
          ...this.form,
        }

        try {
          const { data } = await createWorkOrder(payload)
          console.log('✅ 工单创建成功:', data)
          this.$message.success('工单创建成功！')

          // Close current tab by navigating to another route
          const tagsViewStore = useTagsViewStore()
          tagsViewStore.DEL_VIEW(this.$route)
          this.$router.push('/table/complex')
        } catch (error) {
          console.error('❌ 工单创建失败:', error)
          this.$message.error('工单创建失败')
        }
      })
    },
  },
  watch: {
    'form.production_line_id'(val) {
      this.form.equipment_group_id = null
      this.form.equipment_id = null
      this.form.component_id = null
      if (val) this.fetchEquipmentGroups()
    },
    'form.equipment_group_id'(val) {
      this.form.equipment_id = null
      this.form.component_id = null
      if (val) this.fetchEquipments()
    },
    'form.equipment_id'(val) {
      this.form.component_id = null
      if (val) this.fetchComponents()
    },
    form: {
      handler(val) {
        val.recurrence_type = val.recurrence_setting.recurrence_type
        console.log('form changed:', val)
        console.log('📅 recurrence_setting changed:', val.recurrence_setting)
      },
      deep: true,
    },
  },
}
</script>

<style scoped>
.new-work-order {
  padding: 20px;
}
.work-order-form {
  max-width: 800px;
  margin: 0 auto;
}
.equipment,
.categorization {
  max-width: 800px;
}
.form-item {
  padding-bottom: 10px;
}
.equipment-fields,
.el-input,
.el-select {
  width: 500px;
}
</style>
