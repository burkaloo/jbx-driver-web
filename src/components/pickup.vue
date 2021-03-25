<template>
  <div class="container">
    <scanner/>
  </div>
</template>

<script>
import scanner from '@/components/scanner.vue'
export default {
  name: 'pickup',
  components:{
    scanner
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
button{
  height: 70px;
  font-size: 24px;
}
</style>
