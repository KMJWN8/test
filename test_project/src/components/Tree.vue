<template>
  <div class="relative">
    <div class="font-bold m-2 text-center text-xl border-b-gray-500 text-gray-800">
      Список подразделений
    </div>
    <el-tree
      class="shadow-2 p-4"
      :data="treeData"
      node-key="id"
      :default-expanded-keys="expandedKeys"
      @node-click="handleNodeClick"
      @node-expand="handleNodeExpand"
      @node-contextmenu="handleRightClick"
    />

    <!-- Контекстное меню -->
    <div 
      v-if="contextMenu.visible" 
      class="context-menu"
      :style="{ top: contextMenu.y + 'px', left: contextMenu.x + 'px' }">
        <div class="context-item" @click="openForm('add')">Добавить дочерний узел</div>
        <div class="context-item" @click="openForm('edit')">Редактировать узел</div>
        <div class="context-item" @click="deleteNode">Удалить узел</div>
    </div>

    <!-- Модальное окно с формой -->
    <el-dialog
      v-model="formDialogVisible"
      :title="formTitle"
      width="400px"
      @close="resetForm"
    >
      <el-form :model="form" ref="formRef" label-position="top">
        <el-form-item label="Имя узла" prop="name">
          <el-input v-model="form.name" placeholder="Введите имя узла"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="submitForm">Сохранить</el-button>
          <el-button @click="formDialogVisible = false">Отмена</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue'
import axios from 'axios'

defineProps({
  treeData: Array,
})

const emit = defineEmits(['node-selected', 'refresh-tree'])

const expandedKeys = ref([])
const selectedNode = ref(null)
const contextMenu = ref({ visible: false, x: 0, y: 0 })

const formDialogVisible = ref(false)
const formTitle = ref('')
const formMode = ref('') // 'add' или 'edit'
const form = ref({
name: '',
})

const handleNodeClick = (node) => {
emit('node-selected', node)
}

const handleNodeExpand = (node) => {
expandedKeys.value.push(node.id)
}

const handleRightClick = (event, node) => {
selectedNode.value = node
contextMenu.value = { visible: true, x: event.clientX, y: event.clientY }
}

const openForm = (mode) => {
  formMode.value = mode;
  formTitle.value =
  mode === 'add' ? 'Добавить дочерний узел' : 'Редактировать узел'
  form.value.name = mode === 'edit' ? selectedNode.value.label : ''
  formDialogVisible.value = true
  contextMenu.value.visible = false
};

const submitForm = async () => {
if (formMode.value === 'add') {
  let parentKey = ''
  let childKey = ''
  switch (selectedNode.value.type) {
    case 'service':
      parentKey = 'service'
      childKey = 'department'
      break;
    case 'department':
      parentKey = 'department'
      childKey = 'division'
      break;
    case 'division':
      parentKey = 'division'
      childKey = 'team'
      break;
    default:
      console.log('Неизвестный тип узла')
      return;
  }

const url = `http://127.0.0.1:8000/api/${childKey}s/`
  await axios.post(url, { name: form.value.name, [parentKey]: selectedNode.value.id })
} else if (formMode.value === 'edit') {
    const url = `http://127.0.0.1:8000/api/${selectedNode.value.type}s/${selectedNode.value.id}/`
    await axios.patch(url, { name: form.value.name })
  }
  
  emit('refresh-tree', expandedKeys.value)
    formDialogVisible.value = false
  };
  
  const deleteNode = async () => {
  if (!selectedNode.value) return
    const url = `http://127.0.0.1:8000/api/${selectedNode.value.type}s/${selectedNode.value.id}/`
    await axios.delete(url)
    emit('refresh-tree', expandedKeys.value)
    contextMenu.value.visible = false
  };
  
  const resetForm = () => {
    form.value.name = ''
  };
  
  onMounted(() => {
    document.addEventListener('click', () => {
    contextMenu.value.visible = false
    })
  })

</script>
  <style>
.context-menu {
    position: fixed;
    background: white;
    border: 1px solid #ccc;
    border-radius: 5px;
    box-shadow: 0px 4px 6px rgb(0,0,0,0.1);
    padding: 2px;
    min-width: 200px;
    }
    
    .context-item {
    padding: 8px 16px;
    cursor: pointer;
    }
    
    .context-item:hover {
    background-color: #f4f4f4;
    }

    .el-tree-node__content{
      color: black !important;
    }

    .el-tree-node__expand-icon {
    color: black !important;
    }

    .el-tree {
    --el-tree-node-hover-bg-color: #00ABE1 !important;

    }
  </style>