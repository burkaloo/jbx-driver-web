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
  </div>
</template>

<script>
import axios from 'axios'
import { QrcodeStream } from 'vue3-qrcode-reader'


export default {
  name: 'pickup',
  components:{
    QrcodeStream,
  },
  data(){
    return{
      camera: 'auto',
      scanResult: null,
      beep: new Audio('@/assets/beep.mp3'),
      error: false,
      manual: ""
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

      let comp = this
      axios.post(this.baseurl + "/driverpickup.php", {shipid: content, driverid: this.driverId, log: "with courier"})
      .then(function (response) {
        if(response.data.status == "success"){
            comp.camera = "auto"
        } else{
            comp.error = response.data.response
        }
      })
      .catch(function (error) {
        comp.error = error
      })
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
