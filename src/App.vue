<template type="text/x-template">
  <div id="app">
    <x-grid
      :data-url="'https://api.randomuser.me/'"
      :grid-columns="gridColumns"
      :params-map="{'per_page': 'results', 'page': 'page' }"
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
          label: '姓名',
          elems: [
            { text: '{name.first} {name.last}({id.value})' },
            { text: 'view', href: '/users/{id.value}', target: '_blank' }
          ]
        },
        {
          label: '手机',
          elems: [
            { text: '{phone}' }
          ]
        },
        {
          label: '头像',
          elems: [
            { img: '{picture.thumbnail}', width: '30', href: '/users/{id.value}', target: '_blank' }
          ]
        }
      ],
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
