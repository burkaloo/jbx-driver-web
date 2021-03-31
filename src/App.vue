<template>
  <div>
    <login v-if="page == 'login'" @loadon="spinner = true" @loadoff="spinner = false" :baseurl="baseurl" @user="newuser"/>
    <home v-else-if="page == 'home'" @loadon="spinner = true" @loadoff="spinner = false" @page="gotopage" :baseurl="baseurl" :driverId="user"></home>
    <pickup v-else-if="page == 'pickup'" @loadon="spinner = true" @loadoff="spinner = false" @page="gotopage" :baseurl="baseurl" :driverId="user"/>
    <deliver v-else-if="page == 'deliver'" @page="gotopage" :baseurl="baseurl" :driverId="user" @loadon="spinner = true" @loadoff="spinner = false"/>
    <fail v-else-if="page == 'fail'" @page="gotopage" :baseurl="baseurl" :driverId="user" @loadon="spinner = true" @loadoff="spinner = false"/>
    <ret v-else-if="page == 'ret'" @page="gotopage" :baseurl="baseurl" :driverId="user" @loadon="spinner = true" @loadoff="spinner = false"/>
  </div>

  <modal v-if="spinner">
    <div style="max-width: 100vw;" class="text-center">
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
import deliver from '@/components/deliver.vue'
import fail from '@/components/fail.vue'
import ret from '@/components/ret.vue'


//import axios from 'axios'

export default {
  name: 'App',
  components: {
    login, modal, home, pickup, deliver, fail, ret
  },
  data(){
    return{
      user: null,
      page: "login",
      spinner: false,
      baseurl: "https://shiplabel.navitag.net/vercel-test",
      //baseurl: "https://james.local/jbx/backend"
    }
  },
  created(){
    this.user = sessionStorage.getItem("navitagDriver")
    if(this.user != null){
      this.page = 'home'
    }
  },
  mounted(){
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
