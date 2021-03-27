<template>
  <div>
    <p class="mb-0 mt-2">Last Scan: <span class="ms-2">{{scanResult}}</span></p>
    <qrcode-stream :camera="camera" @decode="onDecode" @init="camInit">
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
    <div class="text-end">
      <button class="btn btn-primary mt-3" @click="manualcheck">Check Package</button>
    </div>
  </div>
</template>

<script>
import { QrcodeStream } from 'vue3-qrcode-reader'
import axios from 'axios'

export default {
  name: 'deviler-scan',
  components: {
    QrcodeStream
  },
  props:{
    baseurl: String,
    driverId: {}
  },
  data(){
    return {
      camera: 'auto',
      scanResult: null,
      beep: new Audio('@/assets/beep.mp3'),
      error: false,
      manual: "",
      toggle: "scan"
    }
  },
  methods:{
    camInit (promise) {
      promise
        .catch(console.error)
        .then()
    },
    onDecode (content) {
      //this.beep.play()
      this.camera = 'off'
      this.scanResult = content

      let comp = this
      axios.post(this.baseurl + "/deliverinfo.php", {shipid: content})
      .then(function (response) {
        if(response.data.status == "success"){
            var rowdata = response.data.response
            if(rowdata.length == 0){
              comp.error = "scaned package is not in database"
            } else if(rowdata[0].status == 'transit'){
                comp.error = "Package not marked for delivery."
            } else if(rowdata[0].status_ref != comp.driverId) {
                comp.error = "Package assigned to different Driver"
            } else{
              //got to detaial
              comp.$emit('details', rowdata[0])
            }
        } else{
            comp.error = response.data.response
        }
      })
      .catch(function (error) {
        console.log(error)
        comp.error = "camera Error"
      })
    },
    manualcheck(){
      this.onDecode(this.manual)
    },
    closeError(){
      this.error =  false
      this.camera = "auto"
    },
    triggertoggle(){
      if(this.toggle == 'scan'){
        this.toggle = "type"
      } else{
        this.toggle = "scan"
      }
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
