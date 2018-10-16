<template>
  <div id="table">
    <div :class="{'table-responsive':responsive}">
      <table :class="classes.table">
        <thead :class="classes.thead">
          <tr>
            <th v-for="(column,key) in columns_norm" :key="key" @click="setSortColumn(key)">{{column.name}}</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(row, key) in data" :key="key">
            <td v-for="(column,key) in columns_norm" :key="key">
              
              <!-- Type is boolean -->
              <template v-if="column.type === 'boolean'">
                <template v-if="booleanIcons.type === 'font-awesome'">
                  <font-awesome-icon v-if="row[column.dataKey[0]]" :icon="booleanIcons.true"/>
                  <font-awesome-icon v-else :icon="booleanIcons.false"/>
                </template>
                <template v-if="booleanIcons.type === 'font-awesome'">
                  <template v-if="row[column.dataKey[0]]">{{booleanIcons.true}}</template>
                  <template v-else>{{booleanIcons.false}}</template>
                </template>
              </template>

              <!-- Type is string -->
              <template v-else>
                <template v-for="key in column.dataKey">{{row[key]}} </template>
              </template>

            </td>
          </tr>
        </tbody>
      </table>
    </div>
    <div class="row">
      <div class="md-6">
        <div class="form-group">
          <label for="rowsPerPage">{{trans.rowsPerPage}}</label>
          <select name="rowsPerPage" id="rowsPerPage" class="form-control" v-model="rowsPerPage">
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
  name: 'Table',
  props: {
    booleanIcons: {
      type: Object,
      default: function(){
        return {
          true: 'X',
          false: ''
        }
      }
    },
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
    data: [Array, Object],
    columns: Array
  },
  computed:{
    columns_norm: function(){
      const columns = []
      this.columns.forEach( (column) => {
        
        const col = {};

        // Add key
        if(typeof column.key === 'undefined' || (typeof column.key !== 'string' && typeof column.key !== 'object')){
          window.console.log(column.key);
          return
        }else if(typeof column.key === 'string'){
          col.dataKey = [column.key]
          col.sortKey = column.key
        }else{
          col.dataKey = column.key
          col.sortKey = column.key[0]
        }

        // Add name
        if(typeof column.name === 'undefined'){
          col.name = column.key
        }else{
          col.name = column.name
        }

        // Add type
        if(typeof column.type === 'undefined' || (column.type !== 'string' && column.type !== 'boolean' && column.type !== 'date')){
          col.type = 'string'
        }else{
          col.type = column.type
        }

        columns.push(col);
        return
      
      });

      return columns
    }
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
