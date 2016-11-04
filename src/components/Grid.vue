<template type="text/x-template">
  <div>
    <table>
      <thead>
      <tr>
        <th v-for="col in columns"
            @click="col.sortKey && sortBy(col.sortKey)"
            :class="{ active: sortKey == col.sortKey }">
          {{ col.label }}
          <span v-show="col.sortKey" class="arrow" :class="sortOrders[col.sortKey] > 0 ? 'asc' : 'dsc'"></span>
        </th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="entry in data"
          @click="rowClick && selectRow(entry)"
          :class="{ selected: entry==selected }">
        <td v-for="col in columns">
          <template v-for="elem in col.elems">
            <a v-if="elem.event"
               @click.prevent="elem.event && elem.event(entry)"
               :href="elem.href && expr(elem.href, entry)">
              <img v-if="elem.img" :src="expr(elem.img, entry)" :width="elem.width" :height="elem.height"/>
              <span v-if="elem.text">{{ expr(elem.text, entry) }}</span>
            </a>
            <a v-if="elem.href && !elem.event"
               :href="elem.href && expr(elem.href, entry)"
               :target="elem.target">
              <img v-if="elem.img" :src="expr(elem.img, entry)" :width="elem.width" :height="elem.height"/>
              <span v-if="elem.text">{{ expr(elem.text, entry) }}</span>
            </a>
            <template v-if="!elem.href && !elem.event">
              <img v-if="elem.img" :src="expr(elem.img, entry)" :width="elem.width" :height="elem.height"/>
              <span v-else>{{ expr(elem.text, entry) }}</span>
            </template>
          </template>
        </td>
      </tr>
      </tbody>
    </table>
    <wheel-paging v-if="paging.total"
      :paging="paging"
      :params="params">
    </wheel-paging>
  </div>
</template>

<script>
import WheelPaging from './Paging'
import exprProp from 'expr-prop'

export default {
  name: 'wheel-grid',
  components: {
    WheelPaging
  },
  props: {
    dataUrl: String,
    filterKey: {
      type: String,
      default: ''
    },
    columns: Array,
    params: Object,
    rowClick: {
      type: Function
    }
  },
  data () {
    var sortOrders = {}
    this.columns.forEach(col => {
      if (col.sortKey) {
        sortOrders[col.sortKey] = col.sortOrder ? col.sortOrder : 1
      }
    })
    return {
      data: [],
      count: 200,
      selected: null,
      sortKey: '',
      sortOrders: sortOrders,
      selectRow: this.rowClick ? function (obj) {
        this.selected = obj
        if (this.rowClick) {
          this.rowClick(obj)
        }
      } : null
    }
  },
  computed: {
    paging: function () {
      if (this.count < 0) {
        return {}
      }

      var total = this.count / this.params.per_page + (this.count % this.params.per_page === 0 ? 0 : 1)
      if (this.params.page > total) {
        this.params.page = total
      }

      var first = Math.max(1, this.params.page - 10 + 1)
      var last = Math.min(total, this.params.page + 10 - 1)
      if (last < first) {
        last = first
      }

      while (last - first >= 10) {
        if (this.params.page - first < last - this.params.page) {
          last--
        } else {
          first++
        }
      }
      return {
        now: this.params.page,
        first: first,
        total: total,
        last: last
      }
    }
  },
  created: function () {
    this.fetchData()
  },
  watch: {
    params: {
      handler: 'fetchData',
      deep: true
    }
  },
  filters: {
    capitalize: function (str) {
      return str.charAt(0).toUpperCase() + str.slice(1)
    }
  },
  methods: {
    sortBy: function (key) {
      this.sortOrders[key] = this.sortKey === key ? this.sortOrders[key] * -1 : this.sortOrders[key]
      this.sortKey = key
      this.params.sort_key = key
      this.params.sort_order = this.sortOrders[key]
      this.params.page = 1
    },
    fetchData: function () {
      this.$http.get(this.dataUrl, {params: this.params}).then((response) => {
        this.data = []
        this.count = response.data.count ? response.data.count : 200
        response.data.results.forEach(item => {
          this.data.push(item)
        })
      }, (response) => {
        // error callback
      })
    },
    expr: function (expr, obj) {
      return expr ? exprProp.expr(expr.replace(/{/g, '${'), obj) : null
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

tr.selected td{
  background-color: #eee;
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
