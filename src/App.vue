<template>
  <div id="app">
    <Table :data="tableData" :columns="columns" :classes="classes" @update="getData($event.page, $event.sortColumn, $event.sortOrder)"/>
  </div>
</template>

<script>
import Table from './components/Table.vue'

export default {
  name: 'app',
  components: {
    Table
  },
  data() {
    return {
      classes:{
        table: ['table', 'table-striped'],
        thead: ['thead-dark']
      },
      columns: {
        first_name : 'First Name',
        email : 'E-mail',
        phone : 'Phone number'
      },
      tableData: [
        {
          first_name : 'Robin',
          last_name : 'Migalski',
          email : 'robin@robinmglsk.com',
          phone : '011 22 33 32'
        },
        {
          first_name : 'Ebe',
          last_name : 'Migalski',
          email : 'Ebe@robinmglsk.com',
          phone : '011 22 33 31'
        },
        {
          first_name : 'Jolien',
          last_name : 'Coemans',
          email : 'jolien@robinmglsk.com',
          phone : '011 22 33 33'
        }
      ]
    }
  },
  methods:{
    getData: function(page, sortColumn, sortOrder){
      window.console.log(page+sortColumn+sortOrder);
      window.console.log('Fetched data from sever');
    }
  }
}
</script>

