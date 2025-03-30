<template>
  <div class="flex gap-4 p-4 bg-white border rounded-lg shadow-md">
    <!-- Фото сотрудника -->
    <div class="flex-shrink-0 w-24 h-24 rounded-full overflow-hidden bg-gray-200 flex items-center justify-center">
  <img
    v-if="employee.photo"
    :src="getPhotoUrl(employee.photo)"
    class="w-full h-full object-cover"
  />
  <span v-else class="text-sm text-gray-500">Нет фото</span>
</div>

    <!-- Информация о сотруднике -->
    <div class="flex-grow">
      <h3 class="text-lg font-bold">{{ employee.full_name }}</h3>
      <p class="text-sm text-gray-600"><strong>Должность:</strong> {{ employee.position }}</p>
      <p class="text-sm text-gray-600"><strong>Дата рождения:</strong> {{ formatDate(employee.date_of_birth) }}</p>
      <p class="text-sm text-gray-600"><strong>Дата начала работы:</strong> {{ formatDate(employee.start_date) }}</p>
      <p class="text-sm text-gray-600"><strong>Место работы:</strong> {{ employee.subdivision_name }}</p>
    </div>
  </div>
</template>

<script setup>
import { defineProps } from 'vue';

const props = defineProps({
  employee: {
    type: Object,
    required: true,
  },
});

// Функция для форматирования даты
const formatDate = (dateString) => {
  const date = new Date(dateString);
  return date.toLocaleDateString('ru-RU', {
    year: 'numeric',
    month: 'long',
    day: 'numeric',
  });
};


const getPhotoUrl = (photoPath) => {
  if (!photoPath) return null; // Если фото отсутствует, возвращаем null
  return photoPath.startsWith('http') ? photoPath : `http://127.0.0.1:8000${photoPath}`;
};
</script>