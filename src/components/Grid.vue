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
                    <th scope="col" v-for="(key, index) in columns" v-bind:key="index"> {{key | capitalize}}</th>
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
          let filterKey = this.searchQuery && this.searchQuery.toLowerCase();
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

          return data;
        }
    },
    filters: {
      capitalize: function (str) {
        return str.charAt(0).toUpperCase() + str.slice(1);
      }
    },
    methods: {
      movePages: function (amount) {
        let newStartRow = this.startRow + (amount * this.rowsPerPage);

        if (newStartRow >= 0 && newStartRow < this.filteredData.length) {
          this.startRow = newStartRow;
        }
      }
    }
  }

</script>
