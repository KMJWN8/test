<template>
    <el-tree
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