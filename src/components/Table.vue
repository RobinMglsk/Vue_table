<template>
    <div id="table">
        <div :class="{'table-responsive':responsive}">
            <table :class="classes.table">
                <thead :class="classes.thead">
                    <tr>
                        <th
                            v-for="(column,key) in columns_norm"
                            :key="key"
                            @click="setSortColumn(column.dataKey[0])"
                        >{{column.name}}</th>
                    </tr>
                </thead>
                <tbody>
                    <tr>
                        <td v-show="loading" :colspan="columns_norm.length" class="loader">
                            <LoadingIndicator aria-busy="true"/>
                        </td>
                    </tr>
                    <tr
                        v-show="!loading"
                        v-for="(row, key) in data"
                        :key="key"
                        :class="{ 'link' : typeof link === 'object'}"
                        @click="followLink(key)"
                    >
                        <td v-for="(column,key) in columns_norm" :key="key">
                            <!-- Type is boolean -->
                            <template v-if="column.type === 'boolean'">
                                <template v-if="booleanIcons.type === 'font-awesome'">
                                    <font-awesome-icon
                                        v-if="getData(row, column.dataKey[0])"
                                        :icon="booleanIcons.true"
                                    />
                                    <font-awesome-icon
                                        v-else-if="booleanIcons.false !== '' && booleanIcons.false !== null"
                                        :icon="booleanIcons.false"
                                    />
                                </template>
                                <template v-else>
                                    <template
                                        v-if="getData(row, column.dataKey[0])"
                                    >{{booleanIcons.true}}</template>
                                    <template v-else>{{booleanIcons.false}}</template>
                                </template>
                            </template>

                            <!-- Type is string -->
                            <template v-else>
                                <template v-for="key in column.dataKey">{{getData(row, key)}} </template>
                            </template>
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>
        <div v-show="!loading" class="row">
            <div class="col-md-6 col-lg-4 col-xl-3">
                <div class="form-group form-row">
                    <div class="col-sm-6">
                        <select
                            name="rowsPerPage"
                            id="rowsPerPage"
                            class="form-control"
                            v-model="rowsPerPage"
                        >
                            <option value="25">25</option>
                            <option value="50">50</option>
                            <option value="100">100</option>
                        </select>
                    </div>
                    <label for="rowsPerPage" class="col-sm-6 col-form-label">{{trans.rowsPerPage}}</label>
                </div>
            </div>
            <div class="col-md-6 offset-lg-2 offset-xl-3">
                <div class="float-right">
                    <Pagination
                        v-if="!loading"
                        :data="pagination"
                        @pagination-change-page="pageChange"
                    />
                </div>
            </div>
        </div>
    </div>
</template>
<script>
import Pagination from "./Pagination";
import LoadingIndicator from "./LoadingIndicator";
export default {
    name: "Table",
    components: { Pagination, LoadingIndicator },
    props: {
        loading: {
            type: Boolean,
            default: false
        },
        link: {
            type: [Object, Boolean],
            default: false
        },
        booleanIcons: {
            type: Object,
            default: function() {
                return {
                    true: "X",
                    false: ""
                };
            }
        },
        responsive: {
            type: Boolean,
            default: true
        },
        classes: {
            type: Object,
            default: function() {
                return {
                    table: "table",
                    thead: ""
                };
            }
        },
        trans: {
            type: Object,
            default: function() {
                return {
                    rowsPerPage: "Items per page"
                };
            }
        },
        data: [Array, Object],
        pagination: {
            type: Object,
            default: function() {
                return {
                    current_page: 1,
                    data: [],
                    from: 1,
                    last_page: 1,
                    next_page_url: null,
                    per_page: 10,
                    prev_page_url: null,
                    to: 1,
                    total: 0
                };
            }
        },
        columns: Array
    },
    computed: {
        columns_norm: function() {
            const columns = [];
            this.columns.forEach(column => {
                const col = {};

                // Add key
                if (
                    typeof column.key === "undefined" ||
                    (typeof column.key !== "string" &&
                        typeof column.key !== "object")
                ) {
                    window.console.log(column.key);
                    return;
                } else if (typeof column.key === "string") {
                    col.dataKey = [column.key];
                    col.sortKey = column.key;
                } else {
                    col.dataKey = column.key;
                    col.sortKey = column.key[0];
                }

                // Add name
                if (typeof column.name === "undefined") {
                    col.name = column.key;
                } else {
                    col.name = column.name;
                }

                // Add type
                if (
                    typeof column.type === "undefined" ||
                    (column.type !== "string" &&
                        column.type !== "boolean" &&
                        column.type !== "date")
                ) {
                    col.type = "string";
                } else {
                    col.type = column.type;
                }

                columns.push(col);
                return;
            });

            return columns;
        }
    },
    data() {
        return {
            sortColumn: Object.keys(this.columns)[0],
            sortOrder: "DESC",
            page: 1,
            rowsPerPage: 25
        };
    },
    watch: {
        rowsPerPage: function() {
            this.emitUpdate();
        }
    },
    methods: {
        setSortColumn: function(column) {
            if (this.sortColumn === column) {
                this.flipSortOrder();
            }

            this.sortColumn = column;
            this.emitUpdate();
        },
        flipSortOrder: function() {
            this.sortOrder = this.sortOrder === "DESC" ? "ASC" : "DESC";
        },
        pageChange(page) {
            this.page = page;
            this.emitUpdate();
        },
        followLink: function(rowId) {
            if (
                typeof this.link === "object" &&
                typeof this.link.name === "string"
            ) {
                const params = {};

                Object.keys(this.link.params).forEach(key => {
                    if (typeof this.link.params[key] === "string") {
                        if (
                            typeof this.data[rowId][this.link.params[key]] !==
                            "undefined"
                        ) {
                            params[key] = this.data[rowId][
                                this.link.params[key]
                            ];
                        } else {
                            params[key] = key;
                        }
                    } else if (
                        typeof this.link.params[key] === "object" &&
                        typeof this.link.params[key].static !== "undefined"
                    ) {
                        if (this.link.params[key].static) {
                            params[key] = this.link.params[key].value;
                        } else {
                            params[key] = this.data[rowId][
                                this.link.params[key].value
                            ];
                        }
                    }
                });

                window.console.log(params);
                this.$router.push({
                    name: this.link.name,
                    params
                });
            }
        },
        getData(data, key) {
            const parts = key.split(".");

            if (parts.length > 1) {
                let value = data;

                for (let i in parts) {
                    if (i < parts.length) {
                        if (typeof value[parts[i]] !== "undefined") {
                            value = value[parts[i]];
                        } else {
                            return null;
                        }
                    }
                }

                return value;
            }

            return data[key];
        },
        emitUpdate: function() {
            this.$emit("update", {
                sortColumn: this.sortColumn,
                sortOrder: this.sortOrder,
                page: this.page,
                rowsPerPage: this.rowsPerPage
            });
        }
    }
};
</script>
<style lang="scss" scoped>
.link {
    cursor: pointer;
}
.loader {
    background: white;
    text-align: center;
}
</style>