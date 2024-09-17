<template>
  <el-radio-group v-model="modelAsString" :disabled="props.disabled">
    <el-radio v-for="data in dataList" :key="data.dictValue" :value="data.dictValue">{{ data.dictLabel }}</el-radio>
  </el-radio-group>
</template>

<script setup lang="ts" name="MaDictRadio">
import { useAppStore } from '@/store/modules/app';
import { getDictDataList } from '@/utils/tool';
import { computed } from 'vue';

const appStore = useAppStore();
const props = defineProps({
  dictType: {
    type: String,
    required: true
  },
  disabled: {
    type: Boolean,
    required: false,
    default: () => false
  }
});

// NOTE: defineModel 语法糖是以下的简写
// const model = defineModel();
// -----------
// const props = defineProps(['modelValue']);
// const emit = defineEmits(['update:modelValue']);
// 使用 defineModel 返回一个ref ， model.value 相当于建立了与 modelValue的联系
// 更改model.value 父组件的 v-model = 'foo' foo值也会改变 父组件更新。
// -----------------
// v-model="foo" 等价于 v-model:modelValue="foo" @update:modelValue="foo = $event"
const model = defineModel<number | string>();

const modelAsString = computed({
  get() {
    return model.value + '';
  },
  set(value) {
    model.value = value;
  }
});

const dataList = getDictDataList(appStore.dictList, props.dictType);
</script>
