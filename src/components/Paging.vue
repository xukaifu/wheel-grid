<template>
  <ul>
    <li v-if="paging.first<paging.now && paging.first>1" @click="params.page=1">首页</li>
    <li :disabled="paging.now==1" @click="params.page=paging.now-1">上一页</li>
    <li v-for="n in paging.last-paging.first"
        :disabled="paging.now == n+paging.first"
        @click="params.page=n+paging.first">{{ n + paging.first }}</li>
    <li :disabled="paging.now==paging.last" @click="params.page = paging.now+1">下一页</li>
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

li{
  display: list-item;
  cursor: pointer;
  float: left;
}
</style>
