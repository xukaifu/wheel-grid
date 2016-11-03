<template type="text/x-template">
  <div>
    <table>
      <thead>
      <tr>
        <th v-for="col in columns"
            @click="sortBy(col.key)"
            :class="{ active: sortKey == col.key }">
          {{ col.label }}
          <span class="arrow" :class="sortOrders[col.key] > 0 ? 'asc' : 'dsc'"></span>
        </th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="entry in rows">
        <td v-for="col in columns">
          <span v-for="elem in col.elems">
            <a v-if="elem.href" :href="expr(elem.href, entry)" :target="elem.target">
              <img v-if="elem.img" :src="expr(elem.img, entry)" :width="elem.width" :height="elem.height"/>
              <span v-if="elem.text">{{ expr(elem.text, entry) }}</span>
            </a>
            <span v-if="!elem.href">{{ expr(elem.text, entry) }}</span>
          </span>
        </td>
      </tr>
      </tbody>
    </table>
    <ul>
      <li @click="nextPage">Next</li>
      <li @click="prevPage">Prev</li>
    </ul>
  </div>
</template>

<script>
const exprProp = require('expr-prop')

export default {
  name: 'x-grid',
  replace: true,
  props: {
    dataUrl: String,
    filterKey: {
      type: String,
      default: ''
    },
    gridColumns: Array
  },
  data () {
    var sortOrders = {}
    var columns = []
    this.gridColumns.forEach(function (col) {
      console.dir(col)
      if (typeof col === 'string') {
        columns.push({key: col, name: col, href: 'h', label: col.charAt(0).toUpperCase() + col.slice(1)})
      } else {
        columns.push(col)
      }
      sortOrders[col.key] = 1
    })
    return {
      sortKey: '',
      sortOrders: sortOrders,
      rows: [],
      ver: 0,
      params: {results: 10, page: 1},
      columns: columns,
      expr: function (expr, obj) {
        console.dir(expr)
        console.dir(obj)
        return exprProp.expr(expr.replace(/{/g, '${'), obj)
      }
    }
  },
  created: function () {
    this.fetchData()
  },
  watch: {
    ver: 'fetchData'
  },
  filters: {
    capitalize: function (str) {
      return str.charAt(0).toUpperCase() + str.slice(1)
    },
    datalize: function (str, entry, col) {
      if (!col.filter) {
        return str
      }

      switch (col.filter) {
        case 'text':
          return str
      }
      // value : text,  html,  number,  date,  img(size),
      // value + href :  href(text, url),
      // value + action, href?  : text_function(param),
      // {href: [{value:'string or function', href:'string? or function' }], actions: [{value:'', function:'' }]}
      // property computed: eval?
      return col.key + '<a href="">href</a>' + str
    }
  },
  methods: {
    sortBy: function (key) {
      this.sortKey = key
      this.sortOrders[key] = this.sortOrders[key] * -1
    },
    fetchData: function () {
      this.$http.get(this.dataUrl, {params: this.params}).then((response) => {
        this.rows = []
        response.data.results.forEach(item => {
          this.rows.push(item)
        })
      }, (response) => {
        // error callback
      })
    },
    nextPage: function () {
      this.params['page'] = this.params['page'] + 1
      this.ver++
    },
    prevPage: function () {
      this.params['page'] = this.params['page'] - 1
      this.ver++
    }
  }
}

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  table {
  border: 2px solid #42b983;
  border-radius: 3px;
  background-color: #fff;
}

th {
  background-color: #42b983;
  color: rgba(255,255,255,0.66);
  cursor: pointer;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}

td {
  background-color: #f9f9f9;
}

th, td {
  min-width: 120px;
  padding: 10px 20px;
}

th.active {
  color: #fff;
}

th.active .arrow {
  opacity: 1;
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
  border-bottom: 4px solid #fff;
}

.arrow.dsc {
  border-left: 4px solid transparent;
  border-right: 4px solid transparent;
  border-top: 4px solid #fff;
}




</style>
