<template>
  <div class="org-container">
    <el-input v-model="orgName" clearable placeholder="请输入关键字过滤" />
    <el-tree
      ref="treeRef"
      :data="orgList"
      :expand-on-click-node="false"
      :filter-node-method="filterNode"
      :props="{ label: 'name', children: 'children' }"
      highlight-current
      node-key="id"
      :current-node-key="currentNodeKey"
      @node-click="handleNodeClick"
    />
  </div>
</template>

<script lang="ts" setup>
import { ElTree } from 'element-plus';
import { useOrgListApi } from '@/api/sys/orgs';
import { onMounted, ref, watch } from 'vue';

// NOTE:  const orgList = ref(); 此时orgList为undefined
const orgList = ref();
const orgName = ref();
const treeRef = ref();
const currentNodeKey = ref();

onMounted(async () => {
  await getOrgList();
});

watch(orgName, val => {
  treeRef.value.filter(val);
});

// 机构列表
const getOrgList = async () => {
  const res = await useOrgListApi();
  orgList.value = res.data;
};

const filterNode = (name: string, data: any) => {
  if (!name) {
    return true;
  }
  return data.name.includes(name);
};

const emits = defineEmits(['nodeClick']);
// 处理点击事件
// NOTE: 这里处理逻辑，如果点击的节点和当前选中的节点一致，则取消选中，否则选中当前节点 存疑？？
const handleNodeClick = (row: any) => {
  if (currentNodeKey.value === row.id) {
    currentNodeKey.value = null;
  } else {
    currentNodeKey.value = row.id;
  }
  emits('nodeClick', currentNodeKey.value);
};
</script>

<style lang="scss" scoped>
.org-container {
  min-height: calc(100vh - 112px - var(--theme-header-height));
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;

  :deep(.el-tree) {
    margin-top: 20px;
  }
}
</style>
