<template type="text/x-template">
  <div id="app">
    <input type="text" :value="params.keyword" @keyup.enter="params.keyword = $event.target.value; params.page = 1"/>
    <x-grid
      :data-url="'https://api.randomuser.me/'"
      :columns="gridColumns"
      :params="params"
      :params-map="{'per_page': 'results', 'page': 'page', 'order_by': 'ob', 'keyword': 'k' }"
      :row-click="warn">
    </x-grid>
  </div>
</template>

<script>
import XGrid from './components/XGrid'

export default {
  name: 'app',
  components: {
    XGrid
  },
  data () {
    return {
      gridColumns: [
        {
          label: 'Text & Link',
          sortKey: 'name',
          elems: [
            { text: '{name.first} {name.last}({id.value})' },
            { text: 'view', href: '/users/{id.value}', target: '_blank' }
          ]
        },
        {
          label: 'Event',
          sortKey: 'age',
          elems: [
            { text: '{dob}', event: function (obj) { console.dir(obj) }, href: 'javascript:void' }
          ]
        },
        {
          label: 'Text',
          elems: [
            { text: '{phone}' }
          ]
        },
        {
          label: 'Image link',
          elems: [
            { img: '{picture.thumbnail}', width: '30', href: '/users/{id.value}', target: '_blank' }
          ]
        },
        {
          label: 'Image',
          elems: [
            { img: '{picture.thumbnail}', width: '30' }
          ]
        }
      ],
      keyword: '',
      params: {results: 10, page: 1, keyword: 'a', sort_key: '', sort_order: 1},
      warn: function (obj) {
        console.dir('xxx')
        console.dir(obj)
      }
    }
  }
}

</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
