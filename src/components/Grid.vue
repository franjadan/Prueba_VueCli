<template>
  <div>
    <form class="text-left" id="search"> 
        <label for="query" class="mr-3">Search</label>
        <input id="query" class="form-control w-25 d-inline" name="query" v-model="searchQuery"></form>
    <div id="grid-template">
      <div>
        <table class="table mt-5">
            <thead>
                <tr>
                    <th scope="col" v-for="(key, index) in columns" v-bind:key="index" @click="sortBy(keys[index])" :class="{ active: sortKey == key }"> {{key | capitalize}}<span class="arrow" :class="sortOrders[key] > 0 ? 'asc' : 'dsc'"></span></th>
                </tr>
            </thead>
            <tbody>
                <tr scope="row" v-for="(entry, index) in dataPerPage" v-bind:key="index">
                <td v-for="(key, index) in keys" v-bind:key="index"> 
                    {{entry[key]}}
                </td>
            </tr>
            </tbody>
        </table>
      </div>
    </div>
    <div id="page-navigation" class="row d-flex flex-row justify-content-around align-items-center mt-5">
        <button class="btn btn-primary" @click=movePages(-1)>Back</button>
        <span>{{startRow / rowsPerPage+1 }} out of {{Math.ceil(filteredData.length / rowsPerPage)}}</span>
        <button class="btn btn-primary" @click=movePages(1)>Next</button>
    </div>
  </div>
</template>
<script>

  export default {

    name: "grid",
    props: {
      data: Array,
      columns: Array,
      keys: Array
    },
    data() {
      return {

        searchQuery: '',
        sortKey: '',
        sortOrders: {},
        startRow: 0,
        rowsPerPage: 10
      }
    },
    computed: {
      dataPerPage: function () {
        return this.filteredData.filter((item, index) => index >= this.startRow && index < (this.startRow + this
          .rowsPerPage))
      },
      filteredData: function () {
          let sortKey = this.sortKey;
          let filterKey = this.searchQuery && this.searchQuery.toLowerCase();
          let order = this.sortOrders[sortKey] || 1;
          let data = this.data;

          if (filterKey) {
            data = data.filter(function (row) {
                return Object.keys(row).some(function (key) {
                    return String(row[key]).toLowerCase().indexOf(filterKey) > -1;
                  }

                )
              }

            )
          }

          if (sortKey) {
            data = data.slice().sort(function (a, b) {
                a = a[sortKey];
                b = b[sortKey];
                return (a === b ? 0 : a > b ? 1 : -1) * order;
              }

            )
          }

          return data;
        }
    },
    filters: {
      capitalize: function (str) {
        return str.charAt(0).toUpperCase() + str.slice(1);
      }
    },
    methods: {
      sortBy: function (key) {
          this.sortKey = key;
          this.sortOrders[key] = this.sortOrders[key] * -1
        },
      movePages: function (amount) {
        let newStartRow = this.startRow + (amount * this.rowsPerPage);

        if (newStartRow >= 0 && newStartRow < this.filteredData.length) {
          this.startRow = newStartRow;
        }
      }
    },
    created() {
      console.log(this.filteredData);

      let sortOrders = {};

      this.columns.forEach(function (key) {
          sortOrders[key] = 1;
        }

      ); this.sortOrders = sortOrders;
    }
  }

</script>
<style scoped>

    th{
        cursor:pointer;
    }
  .arrow {
    display: inline-block;
    vertical-align: middle;
    width: 0;
    height: 0;
    margin-left: 5px;
    opacity: 0.66;
  }

  .arrow.asc {
    border-left: 4px solid transparent;
    border-right: 4px solid transparent;
    border-bottom: 4px solid #FAE042;
  }

  .arrow.dsc {
    border-left: 4px solid transparent;
    border-right: 4px solid transparent;
    border-top: 4px solid #FAE042;
  }

</style>
