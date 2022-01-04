<template>
  <div class="wrap">
    <h1>Smart Energy Conservation</h1>
    <div class="container">
      <line-chart></line-chart>
    </div>
  </div>
</template>

<script>

import LineChart from '@/components/LineChart'

export default {
  components: {
    LineChart
  },
  data() {
    return {
      powerUsage: []
    }
  }, 
  watch: {
    powerUsage(newVal) {
      console.log(newVal)
    }
  }
}
</script>

<style lang="scss" scoped>
.container {
}
</style>
