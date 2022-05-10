# Vue-table

A [Vue.js](https://vuejs.org) table component that plays nicely with [Laravel](https://laravel.com/) resources.

# Getting Started

```
npm i @robinmglsk/vue_table
```

Import and use Vue-table as a component.

```js
<template>
    <Table
        :data="tableData"
        :columns="columns"
        :link="link"
        :classes="classes"
        :pagination="pagination"
        :filters="filters"
        filterable
        @update="getData($event.page, $event.rowsPerPage, $event.sortColumn, $event.sortOrder, $event.filters)"
    />
</template>
<script>
import Table from '@robinmglsk/vue_table'
export default {
  components: {
    Table
  },
  data(){
      return {
          tableData: [],
          link: {
                name: "page-name",
                params: {
                    id: "id",
                }
            },
            classes: {
                table: ["table", "table-striped", "table-hover"],
                thead: ["thead-dark"]
            },
            columns: [
                {
                    name: "First Name",
                    key: "first_name"
                },
                {
                    name: "Name",
                    key: ["first_name", "last_name"]
                },
                {
                    name: "Active",
                    key: ["is_activated"],
                    filter_disabled: true,
                    filter: 'showYesOrNo'
                },
                {
                    name: "Object",
                    key: ["object_data.object.item"],
                    filter: 'reverseString'
                }
            ],
            pagination: {
                current_page: 1,
                first_page_url: "https://yourwebsite.com/api/v1/endpoint?page_size=25&order_by=id&sort=ASC&like=null&page=1",
                from: 1,
                last_page: 8,
                last_page_url: "https://yourwebsite.com/api/v1/endpoint?page_size=25&order_by=id&sort=ASC&like=null&page=8",
                next_page_url: "https://yourwebsite.com/api/v1/endpoint?page_size=25&order_by=id&sort=ASC&like=null&page=2",
                path: "https://yourwebsite.com/api/v1/endpoint",
                per_page: "25",
                prev_page_url: null,
                to: 25,
                total: 199
            },
            filters:{
                reverseString(data){
                    if(typeof data !== 'string') return
                    return data.split('').reverse().join('')
                },
                showYesOrNo(data){
                    console.log(data)
                    if(data) return 'Yes'
                    return 'No'
                }
            },
      }
  },
  methods:{
      getData(page, rowsPerPage, sortColumn, sortOrder, filters){
          //Fetch data from your favorite api here!
      }
  }
}
</script>
```

You'll need to use at least the following prop / event handling to initially configure Vue-table

* columns -- the columns you want to display
* pagination -- the pagination meta returned from a Laravel resource
* data -- the data retrieved from the api endpoint
* @update -- trigger an update for the data

# Column Definition
Columns ar defined as an array of objects.
```js

const data = [
    {
        name: "Name", // Display name
        key: ["first_name"] // The key 
    },
    {
        name: "Full name",
        key: ["first_name", "last_name"] // Multiple values will be combined with spaces in between
    },
    {
        name: "Active",
        key: ["is_activated"],
        filter_disabled: true, // Disable search filter on this column
        filter: 'showYesOrNo' // Vue-table allows you to define filters that can be used to apply common formatting.
    },
    {
        name: "Object",
        key: ["company.name"], // Get date from an object
        sort_key: "company_id", // The name of the column that wil be used for sorting (handy if you like to display data from a relation but want to sort on the id in the parent table)
    }
]
```
## Column options
### name
* Type: `String`
* Usage: Name that needs to be displayed for the column

### key
* Type: `String|Array<String>`
* Usage: Key of the data to be displayed. If the string contains dots it wil handle the data as an object.

### sort_key
* Type: `String`
* Usage: Field name that will be used as sortKey value on sort column event.

### filter_disabled
* Type: `Boolean`
* Usage: If false disables the search filter on the column

### filter_values
* Type: `Array<String>`
* Usages: If filter is enabled , it wil display a select dropdown menu with these values instead of a search field.

### filter_key
* Type: `String`
* Usages: Key used to send to the server for filter values.

### filter
* Type: `String`
* Usage: The filter function to be called by Vue-table to format the value of the column to be displayed. The filter function will receive the raw data as the parameter from Vue-table, and it must return the value that Vue-table will use to display in that column. The filter functions themselves are defined as prop filter on the table component.

