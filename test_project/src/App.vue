<template>
  <div class="common-layout">
    <el-container class="flex flex-col">
      <el-header class="bg-[#161F6D] shadow-md flex flex-row">
        <img src="@/assets/icons/icon.png" class="w-10 h-10 ml-8 my-2" />
        <div class="my-3 mx-8 text-2xl font-bold text-amber-50">
            ГИС учета сотрудников
        </div>
      </el-header>
      <el-container class="flex-1">
        <el-aside class="shadow-md">
          <Tree :tree-data="treeData" @node-selected="onNodeSelected" @refresh-tree="fetchData"/>
        </el-aside>
        <el-main>
          <Statistics :statistics="currentStatistics" />
          <div class="scrollable-content">
            <EmployeeList :employees="selectedEmployees" />
          </div>
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
const currentStatistics = ref(null);

const transformData = (apiData) => {
  return apiData.map((service) => ({
    id: service.id,
    label: service.name,
    type: 'service', // Указываем тип узла
    children: service.departments.map((department) => ({
      id: department.id,
      label: department.name,
      type: 'department', // Указываем тип узла
      children: department.divisions.map((division) => ({
        id: division.id,
        label: division.name,
        type: 'division', // Указываем тип узла
        children: division.teams.map((team) => ({
          id: team.id,
          label: team.name,
          type: 'team', // Указываем тип узла
          members: team.members,
        })),
      })),
    })),
  }));
};

const fetchData = async (expandedKeys = []) => {
  try {
    const response = await axios.get('http://127.0.0.1:8000/api/services/');
    treeData.value = transformData(response.data);
    expandedKeys.forEach(key => expandedKeys.value.push(key));
  } catch (error) {
    console.error('Ошибка при получении данных:', error);
  }
};

const onNodeSelected = async (node) => {
  selectedEmployees.value = collectEmployees(node);

  try {
    let url = '';

    // Формируем URL в зависимости от типа узла
    switch (node.type) {
      case 'service':
        url = `http://127.0.0.1:8000/api/services/${node.id}/statistics/`;
        break;
      case 'department':
        url = `http://127.0.0.1:8000/api/departments/${node.id}/statistics/`;
        break;
      case 'division':
        url = `http://127.0.0.1:8000/api/divisions/${node.id}/statistics/`;
        break;
      case 'team':
        url = `http://127.0.0.1:8000/api/teams/${node.id}/statistics/`;
        break;
      default:
        console.error('Неизвестный тип узла:', node.type);
        return;
    }

    const response = await axios.get(url);
    currentStatistics.value = response.data;
  } catch (error) {
    console.error('Ошибка при получении статистики:', error);
    currentStatistics.value = null;
  }
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
.el-aside{
  width: fit-content !important;
  transition: width 0.3s ease-in-out !important;
  
}
.scrollable-content {
  overflow-y: auto; /* Добавляем прокрутку по вертикали */
  height: 100vh;
  overscroll-behavior: contain;
}
.el-main{
  padding-top: 5px !important;
}
.el-container{
  overflow: hidden;
}
</style>