<template>
  <a-drawer v-model:visible="visible" title="角色详情" :width="width >= 600 ? 600 : '100%'" :footer="false">
    <a-descriptions title="基础信息" :column="2" size="large" class="general-description">
      <a-descriptions-item label="ID">{{ dataDetail?.id }}</a-descriptions-item>
      <a-descriptions-item label="数据权限">
        <GiCellTag :value="dataDetail?.dataScope" :dict="data_scope_enum" />
      </a-descriptions-item>
      <a-descriptions-item label="名称">{{ dataDetail?.name }}</a-descriptions-item>
      <a-descriptions-item label="编码">{{ dataDetail?.code }}</a-descriptions-item>
      <a-descriptions-item label="创建人">{{ dataDetail?.createUserString }}</a-descriptions-item>
      <a-descriptions-item label="创建时间">{{ dataDetail?.createTime }}</a-descriptions-item>
      <a-descriptions-item label="修改人">{{ dataDetail?.updateUserString }}</a-descriptions-item>
      <a-descriptions-item label="修改时间">{{ dataDetail?.updateTime }}</a-descriptions-item>
      <a-descriptions-item label="描述" :span="2">{{ dataDetail?.description }}</a-descriptions-item>
    </a-descriptions>
    <a-descriptions
      title="功能权限"
      :column="2"
      size="large"
      class="permission general-description"
      style="margin-top: 20px; position: relative"
    >
      <a-descriptions-item :span="2">
        <a-tree
          :checked-keys="dataDetail?.menuIds"
          :data="menuList"
          default-expand-all
          check-strictly
          checkable
        />
      </a-descriptions-item>
    </a-descriptions>
    <a-descriptions
      v-if="dataDetail?.dataScope === 5"
      title="数据权限"
      :column="2"
      size="large"
      class="general-description"
      style="margin-top: 20px; position: relative"
    >
      <a-descriptions-item :span="2">
        <a-tree
          :checked-keys="dataDetail?.deptIds"
          :data="deptList"
          default-expand-all
          check-strictly
          checkable
        />
      </a-descriptions-item>
    </a-descriptions>
  </a-drawer>
</template>

<script setup lang="ts">
import { useWindowSize } from '@vueuse/core'
import { type RoleDetailResp, getRole as getDetail } from '@/apis/system/role'
import { useDept, useDict, useMenu } from '@/hooks/app'

const { width } = useWindowSize()

const dataId = ref('')
const dataDetail = ref<RoleDetailResp>()
const visible = ref(false)
const { data_scope_enum } = useDict('data_scope_enum')
const { deptList, getDeptList } = useDept()
const { menuList, getMenuList } = useMenu()

// 查询详情
const getDataDetail = async () => {
  const { data } = await getDetail(dataId.value)
  dataDetail.value = data
}

// 打开
const onOpen = async (id: string) => {
  dataId.value = id
  if (!menuList.value.length) {
    await getMenuList()
  }
  if (!deptList.value.length) {
    await getDeptList()
  }
  await getDataDetail()
  visible.value = true
}

defineExpose({ onOpen })
</script>

<style scoped lang="scss">
.permission :deep(.arco-descriptions-item-label-block) {
  padding-right: 0;
}
</style>
