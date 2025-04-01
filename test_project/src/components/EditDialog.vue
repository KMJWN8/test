<template>
    <el-dialog v-model="visible" title="Редактирование узла">
      <el-form :model="form" label-width="120px">
        <el-form-item label="Название узла">
          <el-input v-model="form.name" />
        </el-form-item>
        
        <el-form-item label="Руководитель">
          <el-select 
            v-model="form.leader_id" 
            filterable
            placeholder="Выберите руководителя"
          >
            <el-option
              v-for="employee in employees"
              :key="employee.id"
              :label="employee.full_name"
              :value="employee.id"
            />
          </el-select>
        </el-form-item>
      </el-form>
  
      <template #footer>
        <el-button @click="visible = false">Отмена</el-button>
        <el-button type="primary" @click="handleConfirm">Сохранить</el-button>
      </template>
    </el-dialog>
  </template>
  
  <script setup>
  import { ref, watch, onMounted } from 'vue';
  import axios from 'axios';
  
  const props = defineProps(['node']);
  const emit = defineEmits(['submit']);
  
  const form = ref({ name: '', leader_id: null });
  const employees = ref([]);
  const visible = ref(false);
  
  onMounted(async () => {
    const { data } = await axios.get('/api/employees/');
    employees.value = data;
  });
  
  watch(() => props.node, (node) => {
    if (node) {
      form.value = {
        name: node.label,
        leader_id: node.leader?.id || null
      };
      visible.value = true;
    }
  });
  
  const handleConfirm = () => {
    emit('submit', form.value);
    visible.value = false;
  };
  </script>
  