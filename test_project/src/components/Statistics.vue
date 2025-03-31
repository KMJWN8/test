<template>
    <el-row>
      <el-col :span="6">
        <el-statistic title="Количество сотрудников" :value="268500" />
      </el-col>
      <el-col :span="6">
        <el-statistic title="Средний возраст сотрудников" :value="outputValue" />
      </el-col>
      <el-col :span="6">
        <el-statistic title="Средний стаж работы" :value="562">
          <template #suffix>
            <el-icon style="vertical-align: -0.125em">
              <ChatLineRound />
            </el-icon>
          </template>
        </el-statistic>
      </el-col>
    </el-row>
  </template>
  
  <script lang="ts" setup>
  import { ref } from 'vue'
  import { useTransition } from '@vueuse/core'
  import { ChatLineRound, Male } from '@element-plus/icons-vue'
  
  const source = ref(0)
  const outputValue = useTransition(source, {
    duration: 1500,
  })
  source.value = 172000
  </script>
  
  <style scoped>
  .el-col {
    text-align: center;
  }
  </style>
  