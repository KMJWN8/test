<!-- <template>
  <div class="common-layout">
    <div class="tree-container">
      <Tree :tree-data="treeData" @node-selected="onNodeSelected" />
    </div>
    <div class="employee-container">
      <EmployeeList :employees="selectedEmployees" />
    </div>
  </div>
</template> -->


<template>
  <div class="common-layout">
    <el-container class="h-screen flex flex-col">
      <el-header class="bg-[#181818] shadow-md">
        <div class="my-3 mx-8 text-2xl font-bold text-amber-50">
            ГИС учета сотрудников
        </div>
      </el-header>
      <el-container class="flex-1">
        <el-aside class="h-full w-[200px] shadow-md">
          <Tree :tree-data="treeData" @node-selected="onNodeSelected" />
        </el-aside>
        <el-main>
          <Statistics />
          <EmployeeList :employees="selectedEmployees" />
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>






<script setup>
import { ref } from 'vue';
import axios from 'axios';
import Tree from './components/Tree.vue';
import EmployeeList from './components/EmployeeList.vue';
import Statistics from './components/Statistics.vue';

// Инициализация данных
const treeData = ref([]);
const selectedEmployees = ref([]);

// Преобразование данных API в формат для дерева
const transformData = (apiData) => {
  return apiData.map((service) => ({
    id: service.id,
    label: service.name,
    leader: service.leader,
    children: service.departments.map((department) => ({
      id: department.id,
      label: department.name,
      leader: department.leader,
      children: department.divisions.map((division) => ({
        id: division.id,
        label: division.name,
        leader: division.leader,
        children: division.teams.map((team) => ({
          id: team.id,
          label: team.name,
          leader: team.leader,
          members: team.members, // Список сотрудников группы
        })),
      })),
    })),
  }));
};

// Получение данных с API через Axios
const fetchData = async () => {
  try {
    const response = await axios.get('http://127.0.0.1:8000/api/services/'); // Замените на реальный URL API
    treeData.value = transformData(response.data);
  } catch (error) {
    console.error('Ошибка при получении данных:', error);
  }
};

const onNodeSelected = (node) => {
  selectedEmployees.value = collectEmployees(node);
};

// Рекурсивный сбор сотрудников
const collectEmployees = (node) => {
    const employees = [];
    const seenIds = new Set();

    function recurse(currentNode) {
        if (currentNode.leader) {
            if (!seenIds.has(currentNode.leader.id)) {
                employees.push(currentNode.leader);
                seenIds.add(currentNode.leader.id);
            }
        }

        if (currentNode.members) {
            currentNode.members.forEach(member => {
                if (!seenIds.has(member.id)) {
                    employees.push(member);
                    seenIds.add(member.id);
                }
            });
        }

        if (currentNode.children) {
            currentNode.children.forEach(child => recurse(child));
        }
    }

    recurse(node);
    return employees;
};

// Вызов функции загрузки данных при инициализации
fetchData();
</script>

<style>
.app-container {
  display: flex;
  gap: 20px;
  padding: 20px;
}
.tree-container {
  width: 30%;
}
.employee-container {
  width: 70%;
}
</style>