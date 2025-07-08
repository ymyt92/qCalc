<template>
  <div id="app">
     <div class="header">
        <p>奇门遁甲计算器</p>
     </div>
    <QiMen></QiMen>
  </div>
</template>

<script>
import QiMen from './components/QiMen.vue'

export default {
  name: 'App',
  components: {
    QiMen
  }
}
</script>

<style lang="less">
html,body,#app {
    width: 100%;
    height: 100%;
}
* {
    padding: 0;
    margin: 0;
    box-sizing: border-box;
}
#app {
    padding: 10px;
    .header{
        width: 92vh;
        margin: 10px auto;
    p{
        font-size: 20px;
        font-weight: 500;
    }
}
}

</style>
