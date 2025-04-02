<template>
  <el-tree
  class="shadow-2 p-4"
    :data="treeData"
    node-key="id"
    :default-expanded-keys="expandedKeys"
    @node-click="handleNodeClick"
    @node-expand="handleNodeExpand"
  />
</template>

<script setup>
import { ref } from 'vue';

// Props для получения данных дерева
defineProps({
  treeData: {
    type: Array,
    required: true,
  },
});

// Состояние для хранения развернутых узлов
const expandedKeys = ref([]);

// Эмит события при клике на узел
const emit = defineEmits(['node-selected']);

// Обработка клика по узлу
const handleNodeClick = (node) => {
  emit('node-selected', node);
};

// Обработка разворачивания узла
const handleNodeExpand = (node) => {
  // Очищаем все развернутые узлы
  expandedKeys.value = [];

  // Добавляем только текущий узел в список развернутых
  expandedKeys.value.push(node.id);
};
</script>

<style>
/* .el-tree-node__content {
  width: 100%;
  white-space: normal;
  word-break: break-word;
  overflow-wrap: break-word;
  hyphens: auto;
  margin-top: auto;
  margin-bottom: 6px;
} */

</style>
