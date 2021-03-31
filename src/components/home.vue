<template>
  <div class="container">
    <div class="row">
      <div class="col-12 bg-primary text-end">
        <button class="btn btn-lg text-white" @click="logout">Logout</button>
      </div>
    </div>
    <div class="row mt-3">
      <div class="col-12">
        <h3 class="m-0">{{name}}</h3>
        <h3 class="m-0">{{number}}</h3>
        <hr>
      </div>
      <div class="col-12">
        <h4 class="m-0 d-flex"><span class="flex-grow-1">Packages Left: {{count}}</span> <button class="btn bnt-primary" @click="getcount">🔄</button></h4>
        <hr>
      </div>
    </div>
    <div class="row row-cols-2">
      <div class="col mb-2">
          <button class="btn btn-primary btn-xlg w-100" type="button" @click="$emit('page', 'pickup')">Pickup</button>
      </div>
      <div class="col mb-2">
          <button class="btn btn-primary btn-xlg w-100" type="button" @click="$emit('page', 'deliver')">Deliver</button>
      </div>
      <div class="col mb-2">
          <button class="btn btn-primary btn-xlg w-100" type="button" @click="$emit('page', 'fail')">Fail</button>
      </div>
      <div class="col mb-2">
          <button class="btn btn-primary btn-xlg w-100" type="button" @click="$emit('page', 'ret')">Return</button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'home',
  data(){
    return{
      name: "",
      number: "",
      count: "",
    }
  },
  mounted(){
    this.getcount()
  },
  props:{
    baseurl: String,
    driverId:{}
  },
  methods:{
    logout(){
      sessionStorage.removeItem("navitagDriver");
      this.$emit('page', 'login')
    },
    getcount(){
      this.$emit('loadon')
      let comp = this
      axios.post(this.baseurl + '/driverinfo.php', {
        driverid: this.driverId
      }).then(function(response){
        if(response.data.status == "success" && response.data.response.length > 0){
          comp.name = response.data.response[0].name
          comp.number = response.data.response[0].number
          comp.count = response.data.response[0].packages
          comp.$emit('loadoff')
        }
      }).catch(function(error){
        console.log(error)
      }).then(function(){

      })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.btn-xlg{
  height: 70px;
  font-size: 24px;
}
</style>
