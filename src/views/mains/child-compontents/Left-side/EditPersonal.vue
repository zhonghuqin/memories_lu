<template>
  <a-button type="primary" class="Editbtn" @click="showDrawer"
    >修改个人信息</a-button
  >
<!--  <a-config-provider-->
<!--    :theme="{-->
<!--      token: {-->
<!--        fontSize: '30px',-->
<!--      },-->
<!--    }"-->
<!--  >-->
<!--  <a-style-provider hash-priority="high">-->
    <a-drawer
      title="修改个人信息"
      :width="720"
      :open="open"
      :body-style="{ paddingBottom: '80px' }"
      :header-style="{textAlign: 'center'}"
      :footer-style="{ textAlign: 'right' }"
      @close="onClose"
    >
      <a-form
        ref="formRef"
        :model="form"
        :rules="rules"
        class="form"
        size="large"
        hideRequiredMark
      >
        <a-row :gutter="16">
          <a-col :span="18">
            <a-form-item label="姓名" name="name">
              <a-input v-model:value="form.name" style="border: none" placeholder="请输入名字" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="16">
          <a-col :span="18">
            <a-form-item label="年龄" name="age">
              <a-input v-model:value="form.age" style="border: none" placeholder="请输入年龄" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="16">
          <a-col :span="18">
            <a-form-item label="性别" name="sex">
              <a-select
                v-model:value="form.sex"
                :options="options"
                placeholder="请选择性别"
              >
              </a-select>
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="16">
          <a-col :span="18">
            <a-form-item label="地址" name="address">
              <a-input v-model:value="form.address" style="border: none" placeholder="请输入地址例如：四川.成都" />
            </a-form-item>
          </a-col>
        </a-row>
      </a-form>
      <div class="drawer-footer">
        <a-space class="btn-box">
          <a-button type="primary" @click="sendDataToBackend()" style="width: 140px;height: 58px;font-size: 30px">保存</a-button>
          <a-button @click="onClose" style="width: 140px;height: 58px;font-size: 30px">取消</a-button>
        </a-space>
      </div>
    </a-drawer>
<!--  </a-style-provider>-->

<!--  </a-config-provider>-->

</template>
<script lang="ts" setup>
import { reactive, ref } from 'vue'
import type { Rule } from 'ant-design-vue/es/form'
import type { SelectProps } from 'ant-design-vue'
import {
  Editperson,
  ZHQgetPersonal
} from '@/service/pages/mains/child-components/Left-side/personallnformation'
import { message } from 'ant-design-vue'
const emit = defineEmits(['data-updated'])

interface FormData {
  name: string
  age: string
  sex: string
  address: string
}
const form = reactive<FormData>({
  name: '',
  age: '',
  sex: '',
  address: ''
})
const options = ref<SelectProps['options']>([
  { value: '男', label: '男' },
  { value: '女', label: '女' }
])
const formRef = ref()

const rules: Record<string, Rule[]> = {
  name: [
    {
      required: true,
      message: '请填写您的姓名',
      trigger: 'change'
    }
  ],
  age: [
    {
      required: true,
      message: '请填写您的年龄',
      trigger: 'change'
    }
  ],
  sex: [
    {
      required: true,
      message: '请选择您的性别',
      trigger: 'change'
    }
  ],
  address: [
    {
      required: true,
      message: '请填写您的地址',
      trigger: 'change'
    }
  ]
}
const open = ref<boolean>(false)
const showDrawer = () => {
  open.value = true
  amendMessage()
}

const onClose = () => {
  open.value = false
}

// 获取数据
const amendMessage = async () => {
  const res = await ZHQgetPersonal()
  const resData = res.data[0]
  form.name = resData.name
  form.sex = resData.sex
  form.age = resData.age
  form.address = resData.contion
  emit('data-updated', resData)
}
const sendDataToBackend = async () => {
  const formdata = new FormData()
  formdata.append('name', form.name)
  formdata.append('age', form.age)
  formdata.append('sex', form.sex)
  formdata.append('contion', form.address)
  // const requestData = {
  //   name: form.name,
  //   age: form.age,
  //   sex: form.sex,
  //   contion: form.address
  // }
  await formRef.value.validate()
  const res = await Editperson(formdata)
  console.log(res)
  message.success('修改成功')
  onClose()
  amendMessage()
}
</script>
<style scoped>
@import url('@/assets/css/mains/child-components/Left-side/personallnfor.css');
:global(.ant-drawer .ant-drawer-title) {
  font-size: 30px;
}
</style>
