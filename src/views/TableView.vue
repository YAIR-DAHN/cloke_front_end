<template>
    <div class="Table">
      <ShowTable/>
    </div>
  </template>
  
  <script>
  import ShowTable from '@/components/ShowTable.vue';
  
  export default {
    name: 'TableView',
    components: {
      ShowTable
  
    }
  }
  </script>