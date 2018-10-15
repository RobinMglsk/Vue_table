<template>
  <div id="table">
    <div :class="{'table-responsive':responsive}">
      <table :class="classes.table">
        <thead :class="classes.thead">
          <tr>
            <th v-for="(column,key) in columns" :key="key" @click="setSortColumn(key)">{{column}}</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(row, key) in data" :key="key">
            <td v-for="(column,key) in columns" :key="key">{{row[key]}}</td>
          </tr>
        </tbody>
      </table>
    </div>
    <div class="row">
      <div class="md-6">
        <div class="form-group">
          <label for="rowsPerPage">{{trans.rowsPerPage}}</label>
          <select name="rowsPerPage" id="rowsPerPage" v-model="rowsPerPage">
            <option value="25">25</option>
            <option value="50">50</option>
            <option value="100">100</option>
          </select>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: 'vue-table',
  props: {
    responsive: {
      type: Boolean,
      default: true
    },
    classes: {
      type: Object,
      default: function(){
        return {
          table: 'table',
          thead: ''
        }
      }
    },
    trans: {
      type: Object,
      default: function(){
        return {
          rowsPerPage: 'Items'
        }
      }
    },
    data: Array,
    columns: Object
  },
  data(){
    return {
      sortColumn: Object.keys(this.columns)[0],
      sortOrder: 'DESC',
      page: 1,
      rowsPerPage: 25
    }
  },
  watch:{
    rowsPerPage: function(){
      this.emitUpdate();
    }
  },
  methods: {
    setSortColumn: function(column){

      if(this.sortColumn === column){
        this.flipSortOrder();
      }
      
      this.sortColumn = column;
      this.emitUpdate();
    },
    flipSortOrder: function(){
      this.sortOrder = this.sortOrder === 'DESC' ? 'ASC' :'DESC';
    },
    emitUpdate: function(){
      this.$emit('update', {
        sortColumn: this.sortColumn,
        sortOrder: this.sortOrder,
        page: this.page,
        rowsPerPage: this.rowsPerPage
      })
    }
  }
}
</script>
