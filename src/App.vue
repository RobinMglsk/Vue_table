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
      columns: [
        {
          name: 'First Name',
          type: 'First Name',
          key: 'first_name'
        },
         {
          name: 'Name',
          key: ['first_name','last_name']
        },
         {
          name: 'Active',
          key: ['active'],
          type: 'boolean'
        }
      ],
      tableData: [
        {
          first_name : 'Robin',
          last_name : 'Migalski',
          email : 'robin@robinmglsk.com',
          phone : '011 22 33 32',
          active : true
        },
        {
          first_name : 'Ebe',
          last_name : 'Migalski',
          email : 'Ebe@robinmglsk.com',
          phone : '011 22 33 31',
          active : true
        },
        {
          first_name : 'Jolien',
          last_name : 'Coemans',
          email : 'jolien@robinmglsk.com',
          phone : '011 22 33 33',
          active : false
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

