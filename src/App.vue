<template>
  <div id="app">
    <Table :data="data" :columns="columns" :link="link" :classes="classes" :pagination="pagination" @update="getData($event.page, $event.rowsPerPage, $event.sortColumn, $event.sortOrder)"/>
  </div>
</template>

<script>
import Table from './components/Table.vue'
import testData from './testData'

export default {
  name: 'app',
  components: {
    Table
  },
  data() {
    return {
      link: { name: 'pagen-name', params : { id: 'id', fname: 'first_name'}},
      classes:{
        table: ['table', 'table-striped', 'table-hover'],
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
      pagination: {
        
      },
      data: []
    }
  },
  created(){
    this.getData(1, 25, null, null);
  },
  methods:{
    getData: function(page, itemsPerPage, sortColumn, sortOrder){

      window.console.log(page+itemsPerPage+sortColumn+sortOrder)
      this.pagination.current_page = parseInt(testData[page].current_page)
      this.pagination.from = parseInt(testData[page].from)
      this.pagination.to = parseInt(testData[page].to)
      this.pagination.last_page = parseInt(testData[page].last_page)
      this.pagination.total = parseInt(testData[page].total)
      this.pagination.per_page = parseInt(testData[page].per_page)

      this.data = testData[page].data
    }
  }
}
</script>

