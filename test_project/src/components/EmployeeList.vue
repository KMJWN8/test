<template>
  <div>
    <div class="flex justify-between items-center mb-4">
      <h3 v-if="localEmployees.length" class="text-xl font-bold text-gray-800">Список сотрудников</h3>
      <button
        v-if="canAddEmployee"
        @click="openAddEmployeeModal"
        class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
      >
        Добавить сотрудника
      </button>
    </div>

    <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
      <!-- Карточки сотрудников -->
      <EmployeeCard
        v-for="employee in localEmployees"
        :key="employee.id"
        :employee="employee"
      />
    </div>

    <div v-if="!localEmployees.length" class="text-center text-gray-500">
      <p class="mt-5">Нет сотрудников в этом подразделении.</p>
    </div>

    <!-- Модальное окно для добавления сотрудника -->
    <div v-if="isModalOpen" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center">
      <div class="bg-white p-6 rounded-lg shadow-lg w-[400px]">
        <h2 class="text-xl font-bold mb-4">Добавить сотрудника</h2>
        <form @submit.prevent="addEmployee">
          <div class="mb-4">
            <label class="block text-gray-700">ФИО</label>
            <input
              v-model="newEmployee.full_name"
              type="text"
              class="w-full border border-gray-300 rounded px-3 py-2"
              required
            />
          </div>
          <div class="mb-4">
            <label class="block text-gray-700">Должность</label>
            <input
              v-model="newEmployee.position"
              type="text"
              class="w-full border border-gray-300 rounded px-3 py-2"
              required
            />
          </div>
          <div class="mb-4">
            <label class="block text-gray-700">Дата рождения</label>
            <input
              v-model="newEmployee.date_of_birth"
              type="date"
              class="w-full border border-gray-300 rounded px-3 py-2"
              required
            />
          </div>
          <div class="mb-4">
            <label class="block text-gray-700">Дата начала работы</label>
            <input
              v-model="newEmployee.start_date"
              type="date"
              class="w-full border border-gray-300 rounded px-3 py-2"
              required
            />
          </div>
          <div class="mb-4">
            <label class="block text-gray-700">Группа</label>
            <select
              v-model="newEmployee.team"
              class="w-full border border-gray-300 rounded px-3 py-2"
              required
            >
              <option v-for="team in availableTeams" :key="team.id" :value="team.id">
                {{ team.name }}
              </option>
            </select>
          </div>
          <div class="flex justify-end">
            <button type="submit" class="bg-green-500 hover:bg-green-700 text-white font-bold py-2 px-4 rounded">
              Сохранить
            </button>
            <button
              @click="closeModal"
              class="ml-2 bg-red-500 hover:bg-red-700 text-white font-bold py-2 px-4 rounded"
            >
              Отмена
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue';
import axios from 'axios';
import EmployeeCard from './EmployeeCard.vue';

const props = defineProps({
  employees: {
    type: Array,
    required: true,
  },
  selectedNode: {
    type: Object,
    required: true,
  },
});

// Состояние модального окна
const isModalOpen = ref(false);
const newEmployee = ref({
  full_name: '',
  position: '',
  date_of_birth: '',
  start_date: '',
  team: null,
});

// Вычисляем доступные группы для выбора
const availableTeams = computed(() => {
  if (!props.selectedNode || !props.selectedNode.children) return [];

  const teams = [];
  function collectTeams(node) {
    if (node.type === 'team') {
      teams.push({ id: node.id, name: node.label });
    }
    if (node.children) {
      node.children.forEach(child => collectTeams(child));
    }
  }

  collectTeams(props.selectedNode);
  return teams;
});

// Открытие и закрытие модального окна
const openAddEmployeeModal = () => {
  isModalOpen.value = true;
};

const closeModal = () => {
  isModalOpen.value = false;
  resetForm();
};

// Сброс формы
const resetForm = () => {
  newEmployee.value = {
    full_name: '',
    position: '',
    date_of_birth: '',
    start_date: '',
    team: null,
  };
};

// Создаем локальную копию массива
const localEmployees = ref([...props.employees]);

// Наблюдаем за изменениями props.employees
watch(
  () => props.employees,
  (newEmployees) => {
    localEmployees.value = [...newEmployees];
  }
);

// Отправка данных на бэкенд
const addEmployee = async () => {
  try {
    // Шаг 1: Создаем сотрудника через POST
    const createResponse = await axios.post('http://127.0.0.1:8000/api/employees/', {
      full_name: newEmployee.value.full_name,
      position: newEmployee.value.position,
      date_of_birth: newEmployee.value.date_of_birth,
      start_date: newEmployee.value.start_date,
    });

    const employeeId = createResponse.data.id; // Получаем ID созданного сотрудника

    // Шаг 2: Добавляем сотрудника в выбранную группу через PATCH
    const teamId = newEmployee.value.team;
    await axios.patch(`http://127.0.0.1:8000/api/teams/${teamId}/add-member/`, {
      member_id: employeeId,
    });
    // Добавляем сотрудника в локальный список
    localEmployees.value.push(createResponse.data);

    // Закрыть модальное окно и очистить форму
    closeModal();

    // Обновить список сотрудников
    emit('refresh-employees');
  } catch (error) {
    console.error('Ошибка при добавлении сотрудника:', error);
  }
};

// Проверка возможности добавления сотрудника
const canAddEmployee = computed(() => {
  return availableTeams.value.length > 0;
});
</script>