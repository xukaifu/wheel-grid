<template>
  <div id="app">
    <x-grid
      :data-url="'https://api.randomuser.me/'"
      :grid-columns="gridColumns"
      :params-map="{'per_page': 'results', 'page': 'page' }">
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
            { type: 'text', text: '{name.first} {name.last}' },
            { type: 'img', src: 'picture.thumbnail', width: '50', height: '50', href: '/users/{id}', target: '_blank' },
            { type: 'link', href: '/users/{id}', target: '_blank' },
            { type: 'event', event: 'warn', target: '_blank' }
          ]
        },
        {
          label: '手机',
          elems: [
            { type: 'text', text: 'phont' },
            { type: 'img', src: 'picture.thumbnail', width: '50', height: '50', href: '/users/{id}', target: '_blank' },
            { type: 'link', href: '/users/{id}', target: '_blank' },
            { type: 'event', event: 'warn', target: '_blank' }
          ]
        }
      ]
    }
  },
  created: {
    warn: function () {
      console.log(1)
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
