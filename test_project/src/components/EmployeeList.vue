<template>
    <div>
      <h3 v-if="employees.length" class="text-xl font-bold mb-4 text-gray-800">Список сотрудников</h3>
      <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
        <!-- Карточки сотрудников -->
        <EmployeeCard
          v-for="employee in employees"
          :key="employee.id"
          :employee="employee"
        />
      </div>
      <div v-if="!employees.length" class="text-center text-gray-500">
        <p class="mt-5">Нет сотрудников в этом подразделении.</p>
      </div>
    </div>
  </template>
  
  <script setup>
  import EmployeeCard from './EmployeeCard.vue';
  
  defineProps({
    employees: {
      type: Array,
      required: true,
    },
  });
  </script>