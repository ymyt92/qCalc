<template>
    <div id="app">
        <QiMen ref="qiMen"></QiMen>
    </div>
</template>

<script>
import QiMen from './components/QiMen.vue'
export default {
    components: {
        QiMen
    }
}
</script>

<style lang="less">
html,
body,
#app {
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
}
</style>
