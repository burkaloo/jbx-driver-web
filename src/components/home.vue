<template>
  <div class="container">
    <div class="row row-cols-2">
      <div class="col">
        <div class="d-grid gap-2">
          <button class="btn btn-primary" type="button" @click="$emit('page', 'pickup')">Pickup</button>
        </div>
      </div>
      <div class="col">
        <div class="d-grid gap-2">
          <button class="btn btn-primary" type="button" @click="$emit('page', 'deliver')">Deliver</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'home',
  props: {
    msg: String
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
