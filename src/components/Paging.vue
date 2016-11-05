<template>
  <ul>
    <li v-if="paging.first<paging.now && paging.first>1" @click="params.page=1">首页</li>
    <li :disabled="paging.now==1"
        :class="{disabled: paging.now==1}"
        @click="params.page=paging.now-1">上一页</li>
    <li v-for="n in paging.last-paging.first"
        :disabled="paging.now == n+paging.first"
        :class="{active: paging.now == n+paging.first}"
        @click="params.page=n+paging.first">{{ n + paging.first }}</li>
    <li :disabled="paging.now==paging.last"
        :class="{disabled: paging.now==paging.last}"
        @click="params.page = paging.now+1">下一页</li>
    <li v-if="paging.now<paging.last && paging.last<paging.total" @click="params.page=paging.total">末页</li>
  </ul>
</template>

<script>
  export default{
    name: 'wheel-paging',
    props: {
      paging: Object,
      params: Object
    }
  }
</script>

<style scoped>
ul{
  display: block;
  list-style-type: none;
}
ul li {
  color: #42b983;
  margin: 0 3px;
  padding: 4px 15px;
  border: 1px #42b983 solid;
  display: list-item;
  cursor: pointer;
  float: left;
}
ul li:hover,
ul li.active {
  background-color: #42b983;
  color: #fff;
}
ul li.disabled,
ul li.disabled:hover {
  border: 1px #42b983 solid;
  background-color: #fff;
  color: #42b983;
  opacity: .3;
  cursor: not-allowed;
}
</style>
