<template>
  <div class="container">
    <modal v-if="msg" @bgclick="dismiss">
    </modal>
    <div class="row bg-primary">
      <div class="col-12">
          <button class="btn text-white" @click="$emit('page', 'home')">BACK</button>
      </div>
    </div>
    <div class="row py-3">
      <div class="col-12">
        <retscan v-if="stage == 'scan'" :baseurl="baseurl" :driverId="driverId" @details="gotodetails"/>
        <drawpad v-else-if="stage == 'sig'" @img="savesig" @cancel="stage = 'details'"/>
        <div v-else-if="stage == 'details'">
        <div>
          <h4>Package Details</h4>
          <p>{{r_name}}</p>
          <p>{{r_company}}</p>
          <p v-if="cod > 0">COD: {{cod}}</p>
        </div>
        <hr>
        <div>
          <label class="form-label">Receiver Name:</label>
          <input class="form-control mb-3" type="text" v-model.trim ="receiver"/>
          <button v-if="sig == null" class="btn btn-danger btn-block w-100" @click="getsig">Get Signiture</button>
          <button v-else class="btn btn-success w-100" @click="getsig">Repeat Signiture</button>
        </div>
        <hr>
        <button class="btn btn-primary btn-lg w-100 mt-4" :disabled="delivercheck" @click="markdeliverd">Mark Returned</button>
      </div>
    </div>
    </div>
  </div>
</template>

<script>

import axios from 'axios'
import drawpad from '@/components/drawpad.vue'
import retscan from '@/components/retscan.vue'
import modal from '@/components/modal.vue'

export default {
  name: 'deliver',
  components:{
    drawpad, retscan, modal
  },
  data(){
    return {
      stage: 'scan',
      cod: 0,
      r_name: "",
      r_company: "",
      shipid: "",
      sig: null,
      receiver: "",
      msg : false
    }
  },
  watch:{
  },
  props:{
    baseurl: String,
    driverId: {}
  },
  methods: {
    gotodetails(data){
      this.r_name = data.to_name
      this.r_company = data.tp_company
      this.shipid = data.id
      this.cod = isNaN(parseInt(data.cod)) ? 0 : parseInt(data.cod)
      this.stage = 'details'
    },
    savesig(img){
      this.sig = img
      this.stage ="details"
      console.log(img)
    },
    getsig(){
      this.stage = "sig"
    },
    markdeliverd(){
      this.$emit('loadon')
      let comp = this
      let reqerror = false
      axios.post(this.baseurl + "/markreturn.php", {
        shipid: this.shipid,
        driverid: this.driverId,
        signature: this.sig,
        receiver: this.receiver
      })
      .then(function (response) {
        if(response.data.status == "success"){
          comp.cod = 0
          comp.amount= 0
          comp.r_name= ""
          comp.r_company=""
          comp.shipid = ""
          comp.sig= null
          comp.receiver= ""
          comp.stage= 'scan'
        } else{
            reqerror = response.data.response
        }
      })
      .catch(function (error) {
        reqerror = error
      })
      .then(function(){
        this.$emit('loadoff')
        if(reqerror){
          comp.msg = reqerror
        }
      })
    }
  },
  computed:{
    delivercheck(){
      if(this.receiver != "" && this.sig != null){
        return false
      } else {
        return true
      }

    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
