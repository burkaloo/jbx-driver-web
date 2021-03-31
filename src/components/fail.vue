<template>
  <div class="container">
    <div class="row bg-primary">
      <div class="col-12">
        <button class="btn text-white" @click="$emit('page', 'home')">BACK</button>
      </div>
    </div>
    <div class="row">
      <div class="col-12 py-3">
        <div class="">
          <p class="m-0">Last Scan: <span class="ms-2">{{scanResult}}</span></p>
        </div>
        <qrcode-stream :camera="camera" @decode="onDecode" @init="onInit">
          <div v-if="camera == 'off'" class="text-center overlay">
            <div v-if="error" class="px-2">
              <h3 class="text-danger">{{error}}</h3>
              <button class="btn btn-lg btn-primary mt-5" @click="closeError">OK</button>
            </div>
            <div v-else>
              <div class="spinner-border m-5" role="status">
                <span class="visually-hidden">Loading...</span>
              </div>
            </div>
          </div>
        </qrcode-stream>
        <label class="form-label mt-4">Manual Input</label>
        <input class="form-control" type="text" v-model.trim="manual"/>
        <div class="text-end pb-3">
          <button class="btn btn-primary mt-3" @click="manualcheck">Check Package</button>
        </div>
      </div>
    </div>
    <modal v-if="modal">
      <div class="p-3">
        <h4>Reason for Failed Delivery:</h4>
        <div class="form-check">
          <input class="form-check-input" type="radio" value="Address not Found" v-model="reason">
          <label class="form-check-label">
            Address not Found
          </label>
        </div>
        <div class="form-check">
          <input class="form-check-input" type="radio" value="Customer Refused" v-model="reason">
          <label class="form-check-label">
            Customer Refuse
          </label>
        </div>
        <div class="form-check">
          <input class="form-check-input" type="radio" value="No one to receive" v-model="reason">
          <label class="form-check-label" >
            No One to receive
          </label>
        </div>
        <div class="text-center">
          <button class="btn btn-primary" @click="markfail" :disabled="reason == '' || lodaing">
            <div v-if="loading" class="spinner-border m-5" role="status">
              <span class="visually-hidden">Loading...</span>
            </div>
            <span v-else>Confirm</span>
          </button>
        </div>
      </div>
    </modal>
  </div>
</template>

<script>
import axios from 'axios'
import { QrcodeStream } from 'vue3-qrcode-reader'
import modal from '@/components/modal.vue'

export default {
  name: 'pickup',
  components:{
    QrcodeStream, modal
  },
  data(){
    return{
      camera: 'auto',
      scanResult: null,
      beep: new Audio('@/assets/beep.mp3'),
      error: false,
      manual: "",
      reason: "",
      modal: false,
      loading: false,
      lon: 0,
      lat: 0,
      poserr: false
    }
  },
  props:{
    baseurl: String,
    driverId: {}
  },
  methods: {
    onInit (promise) {
      promise
        .catch(console.error)
        .then()
    },
    onDecode (content) {
      //this.beep.play()
      this.camera = 'off'
      this.scanResult = content
      this.modal = true
      this.getPos()
    },
    markfail(){
      if(!this.poserr && this.lon == 0 && this.lat == 0){
        this.error = "please allow gps position logging"
        this.modal = false
      } else{
        let comp = this
        axios.post(this.baseurl + "/driverfail.php", {shipid: this.scanResult, driverid: this.driverId, log: this.reason, lat: this.lat, lon: this.lon})
        .then(function (response) {
          if(response.data.status == "success"){
              comp.camera = "auto"
          } else{
              comp.error = response.data.response
          }
        })
        .catch(function (error) {
          comp.error = error
        }).then(function(){
          comp.modal = false
          comp.reason = ""
          comp.lat = 0
          comp.lon = 0
        })
      }

    },
    parsePos(pos){
      this.lat = pos.coords.latitude
      this.lon = pos.coords.longitude
    },
    getPos(){
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(this.parsePos);
      } else{
        this.poserr = true
      }
    },
    closeError(){
      this.error =  false
      this.camera = "auto"
    },
    manualcheck(){
      this.onDecode(this.manual)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.overlay{
  background-color: rgba(200, 200, 200, .95);
  height: 100%;
  padding-top: 15%;
}
</style>
