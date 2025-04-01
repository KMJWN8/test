<template>
  <div v-if="statistics" class="p-4 bg-gray-100 rounded-lg shadow-md">
    <h3 class="text-lg font-bold mb-2">Статистика</h3>
    <ul class="space-y-2">
      <li><strong>Количество сотрудников:</strong> {{ statistics.employee_count }}</li>
      <li><strong>Средний возраст:</strong> {{ statistics.average_age }} лет</li>
      <li><strong>Средний стаж:</strong> {{ statistics.average_tenure }} года</li>
    </ul>
  </div>
  <div v-else class="p-4 bg-gray-100 rounded-lg shadow-md">
    <p class="text-gray-600">Выберите подразделение для просмотра статистики.</p>
  </div>
</template>

<script setup>
defineProps({
  statistics: {
    type: Object,
    default: null,
  },
});
</script>

<style></style>