<template>
  <div>
    <login v-if="page == 'login'" @loadon="spinner = true" @loadoff="spinner = false" :baseurl="baseurl" @user="newuser"/>
    <home v-else-if="page == 'home'" @loadon="spinner = true" @loadoff="spinner = false" @page="gotopage"></home>
    <pickup v-else-if="page == 'pickup'" @loadon="spinner = true" @loadoff="spinner = false" @page="gotopage"/>
  </div>
  <!--qrcode-stream @decode="onDecode"></qrcode-stream-->

  <modal v-if="spinner">
    <div style="max-width: 100vh;">
      <div class="spinner-border m-5" role="status">
        <span class="visually-hidden">Loading...</span>
      </div>
    </div>
  </modal>
</template>

<script>

import 'bootstrap'
import 'bootstrap/dist/css/bootstrap.min.css'

import login from '@/components/login.vue'
import modal from '@/components/modal.vue'
import home from '@/components/home.vue'
import pickup from '@/components/pickup.vue'

//import axios from 'axios'

export default {
  name: 'App',
  components: {
    login, modal, home, pickup
  },
  data(){
    return{
      user: null,
      page: "login",
      spinner: false,
      baseurl: "http://james.local/jbx/backend/",
    }
  },
  created(){
    this.user = sessionStorage.getItem("navitagDriver")
    if(this.user != null){
      this.page = 'home'
    }
  },
  methods:{
    newuser(id){
      this.user = id
      this.page = "home"
    },
    gotopage(page){
      this.page = page
    }
  }
}
</script>

<style>
</style>
