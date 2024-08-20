<template>
  <div class="input-wrapper" :style="`width: ${tableWidth}px; height: ${rowSize}px;`">
    <input
      v-for="column in columns"
      class="input-range"
      v-model="column.width"
      @change="saveColWidthsToLocaLStorage"
      type="range"
      id="volume"
      name="volume"
      min="50"
      max="200"
      :key="column.key"
    />
  </div>
  <el-table-v2 class="bruh" :columns="columns" :data="data" :width="tableWidth" :height="400" />
</template>

<script setup lang="ts">
import { computed, ref } from 'vue'

const generateColumns = (length = 10, prefix = 'column-', props?: any) =>
  Array.from({ length }).map((_, columnIndex) => {
    const savedColWidths = JSON.parse(localStorage.getItem('columnsWidth') || '[]')
    console.log(savedColWidths[columnIndex])
    return {
      ...props,
      key: `${prefix}${columnIndex}`,
      dataKey: `${prefix}${columnIndex}`,
      title: `Column ${columnIndex}`,
      width: savedColWidths[columnIndex] || 200
    }
  })

const generateData = (columns: ReturnType<typeof generateColumns>, length = 200, prefix = 'row-') =>
  Array.from({ length }).map((_, rowIndex) => {
    return columns.reduce(
      (rowData, column, columnIndex) => {
        rowData[column.dataKey] = `Row ${rowIndex} - Col ${columnIndex}`
        return rowData
      },
      {
        id: `${prefix}${rowIndex}`,
        parentId: null
      }
    )
  })

const columns = ref(generateColumns(10))

const tableWidth = 900

const rowSize = 50

const columnsWidth = computed(() => columns.value.map((column) => column.width))

const saveColWidthsToLocaLStorage = () => {
  localStorage.setItem('columnsWidth', JSON.stringify(columnsWidth.value))
}

const data = generateData(columns.value, 1000)
</script>

<style scoped>
.input-wrapper {
  position: absolute;
  z-index: 1;
  display: flex;
}

.input-range {
  -webkit-appearance: none;
  appearance: none;
  cursor: col-resize;
  outline: none;
  overflow: hidden;
  height: 100%;
  background: none;
  margin: 0;
  flex: 0 1 auto;
}

.input-range::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  height: 50px;
  width: 1px;
  border-left: 1px solid black;
}
</style>
