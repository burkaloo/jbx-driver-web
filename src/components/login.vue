<template>
  <div>
    <div class="container">
      <div class="row">
        <div class="col-12">
          <h2>Driver Login</h2>
        </div>
        <div class="col-12 text-start mt-5">
          <br v-if="msg == ''">
          <p v-else class="m-0 bg-danger text-danger text-center p-2">{{msg}}</p>
          <label class="form-label mt-3">number</label>
          <input class="form-control" maxlength="11" v-model.trim="num" type="text" />
          <label class="form-label">password</label>
          <input class="form-control" type="password" v-model="password" />
        </div>
        <div class="mt-4">
          <div class="d-grid gap-2">
            <button class="btn btn-primary" type="button" @click="login">Login</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'login',
  props:{
    baseurl: String
  },
  data(){
    return{
      num: "",
      password: "",
      msg: ""
    }
  },
  methods:{
    login(){
      let comp = this
      this.$emit('loadon');
      this.msg == ""
      axios.post(this.baseurl + "/driverlogin.php", {
        num: this.num,
        password: this.password
      })
      .then(function (response) {
        if(response.data.status == "success" && response.data.response.length > 0 ){
          sessionStorage.setItem("navitagDriver", response.data.response[0].id);
          comp.$emit("user", response.data.response[0].id);
        } else{
          comp.msg = "invalid login"
        }
      })
      .catch(function (error) {
        console.log(error);
      })
      .then(function (){
        comp.$emit('loadoff')
      })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
