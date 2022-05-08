<template>
    <div id="app">
        <Table
            :data="data"
            :columns="columns"
            :link="link"
            :classes="classes"
            :pagination="pagination"
            :filters="filters"
            filterable
            @update="getData($event.page, $event.rowsPerPage, $event.sortColumn, $event.sortOrder, $event.filters)"
        />
    </div>
</template>

<script>
import Table from "./components/Table.vue";
import testData from "./testData";

export default {
    name: "app",
    components: {
        Table
    },
    data() {
        return {
            link: {
                name: "pagen-name",
                params: {
                    id: "id",
                    fname: "first_name",
                    x: "qsdsqd",
                    y: {
                        static: true,
                        value: "id"
                    },
                    z: {
                        static: false,
                        value: "id"
                    }
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
                    key: ["first_name", "last_name"],
                    sort_key: 'someSortFieldName'
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
            pagination: {},
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
            data: []
        };
    },
    created() {
        this.getData(1, 25, null, null);
    },
    methods: {
        getData: function(page, itemsPerPage, sortColumn, sortOrder, filters) {
            window.console.log(arguments);

            this.pagination.current_page = parseInt(
                testData[page].current_page
            );
            this.pagination.from = parseInt(testData[page].from);
            this.pagination.to = parseInt(testData[page].to);
            this.pagination.last_page = parseInt(testData[page].last_page);
            this.pagination.total = parseInt(testData[page].total);
            this.pagination.per_page = parseInt(testData[page].per_page);

            this.data = testData[page].data;
        }
    }
};
</script>

