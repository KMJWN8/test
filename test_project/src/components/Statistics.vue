<!-- <template>
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
</template> -->

<template>
  <div v-if="statistics" class="p-6 bg-white rounded-lg shadow-2xs border border-gray-200 my-4">
    <h3 class="text-xl font-semibold text-gray-800 mb-4 flex items-center">
      <img src="@/assets/icons/bar_chart.png" alt="Статистика" class="w-6 h-6 mr-2" />
      Статистика подразделения
    </h3>
    <ul class="divide-y divide-gray-200">
      <li class="py-2 flex items-center">
        <img src="@/assets/icons/people.png" alt="Количество сотрудников" class="w-5 h-5 mr-3" />
        <strong class="text-gray-700">Количество сотрудников:</strong>
        <span class="ml-auto text-gray-900 font-medium">{{ statistics.employee_count }}</span>
      </li>
      <li class="py-2 flex items-center">
        <img src="@/assets/icons/cake.png" alt="Средний возраст" class="w-5 h-5 mr-3" />
        <strong class="text-gray-700">Средний возраст:</strong>
        <span class="ml-auto text-gray-900 font-medium">{{ statistics.average_age }} лет</span>
      </li>
      <li class="py-2 flex items-center">
        <img src="@/assets/icons/work.png" alt="Средний стаж" class="w-5 h-5 mr-3" />
        <strong class="text-gray-700">Средний стаж:</strong>
        <span class="ml-auto text-gray-900 font-medium">{{ statistics.average_tenure }} года</span>
      </li>
    </ul>
  </div>

  <div v-else class="p-6 bg-white rounded-lg shadow-lg border border-gray-200">
    <p class="text-gray-600 text-center flex items-center justify-center">
      <img src="@/assets/icons/info.png" alt="Информация" class="w-5 h-5 mr-2" />
      Выберите подразделение для просмотра статистики.
    </p>
  </div>
</template>

<style>

</style>


<script setup>
defineProps({
  statistics: {
    type: Object,
    default: null,
  },
});
</script>

<style></style>