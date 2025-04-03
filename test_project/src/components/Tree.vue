<!-- <template>
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

<style></style> -->

<template>
  <div class="relative">
    <el-tree
      class="shadow-2 p-4"
      :data="treeData"
      node-key="id"
      :default-expanded-keys="expandedKeys"
      @node-click="handleNodeClick"
      @node-expand="handleNodeExpand"
      @node-contextmenu="handleRightClick"
    />
    
  
    <div 
    v-if="contextMenu.visible" 
    class="context-menu"
    :style="{ top: contextMenu.y + 'px', left: contextMenu.x + 'px' }">
        <div class="context-item" @click="addChild">Добавить дочерний узел</div>
        <div class="context-item" @click="editNode">Редактировать узел</div>
        <div class="context-item" @click="deleteNode">Удалить узел</div>
      </div>
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue';
import axios from 'axios';

const props = defineProps({
  treeData: Array,
});
const emit = defineEmits(['node-selected', 'refresh-tree']);

const expandedKeys = ref([]);
const selectedNode = ref(null);
const contextMenu = ref({ visible: false, x: 0, y: 0 });

const handleNodeClick = (node) => {
  emit('node-selected', node);
};

const handleNodeExpand = (node) => {
  expandedKeys.value.push(node.id);
};

const handleRightClick = (event, node) => {
  selectedNode.value = node;
  contextMenu.value = { visible: true, x: event.clientX, y: event.clientY };
};

const addChild = async () => {
  if (!selectedNode.value) return;

  let parentKey = ''
  
  switch(selectedNode.value.type){
    case 'service':
      parentKey = 'service'
      break
    case 'department':
      parentKey = 'service'
      break
    case 'division':
      parentKey = 'department'
      break
    case 'team':
      parentKey = 'division'
      break
    default:
      console.log("Неизвестный тип узла")
      return
  }
  const url = `http://127.0.0.1:8000/api/${parentKey}s/`;
  await axios.post(url, { name: 'Новый узел', [parentKey]: selectedNode.value.id });
  emit('refresh-tree', expandedKeys.value);
  contextMenu.value.visible = false;
  closeContextMenu()
};

const editNode = async () => {
  if (!selectedNode.value) return;
  const newName = prompt('Введите новое имя:', selectedNode.value.label);
  if (!newName) return;
  const url = `http://127.0.0.1:8000/api/${selectedNode.value.type}s/${selectedNode.value.id}/`;
  await axios.patch(url, { name: newName });
  emit('refresh-tree', expandedKeys.value);
  contextMenu.value.visible = false;
  closeContextMenu()
};

const deleteNode = async () => {
  if (!selectedNode.value) return;
  const url = `http://127.0.0.1:8000/api/${selectedNode.value.type}s/${selectedNode.value.id}/`;
  await axios.delete(url);
  emit('refresh-tree', expandedKeys.value);
  contextMenu.value.visible = false;
  closeContextMenu()
};


const closeContextMenu = () => {
  contextMenu.value.visible = false
}

onMounted(() => {
  document.addEventListener('click', closeContextMenu)
})

// onUnmounted(() => {
//   document.removeEventListener('click', closeContextMenu)
// })
</script>

<style>
.el-dropdown {
  position: absolute;
  z-index: 1000;
}

.context-menu{
  position: absolute;
  background: white;
  border: 1px solid #ccc;
  border-radius: 5px;
  box-shadow: 0px 4px 6px rgb(0,0,0,0.1);
  padding: 8pxx 0;
  min-width: 150px;
  z-index: 1000;
}

.context-item{
  padding: 8px 16px;
  cursor: pointer;
  transition: background 0.2s;
}

.context-item:hover{
background: #f4f4f4;
}
</style>