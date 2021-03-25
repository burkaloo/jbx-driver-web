<template>
  <div>
  <p class="decode-result">Last result: <b>{{ result }}</b></p>

  <qrcode-stream :camera="camera" @decode="onDecode" @init="onInit">
    <div v-if="validationSuccess" class="validation-success">
      This is a URL
    </div>

    <div v-if="validationFailure" class="validation-failure">
      This is NOT a URL!
    </div>

    <div v-if="validationPending" class="validation-pending">
      Long validation in progress...
    </div>
  </qrcode-stream>
</div>
</template>

<script>
import { QrcodeStream } from 'vue3-qrcode-reader'
import axios from 'axios'
export default {
  name: 'scanner',
  components:{
    QrcodeStream,
  },
  data(){
    return {
      isValid: undefined,
      camera: 'auto',
      result: null,
    }
  },
  props: {
    baseurl:String
  },
  watch:{
    camera(newval){
      if(newal == "off"){
        this.$emit('loadon')
      } else {
        this.$emit('loadoff')
      }
    }
  }
  methods:{
    onInit (promise) {
      promise
        .catch(console.error)
        .then(this.resetValidationState)
    },

    resetValidationState () {
      this.isValid = undefined
    },

    async onDecode (content) {
      this.result = content
      this.turnCameraOff()

      // pretend it's taking really long
      await this.timeout(3000)
      //check with database

      // some more delay, so users have time to read the message
      await this.timeout(2000)

      this.turnCameraOn()
    },

    turnCameraOn () {
      this.camera = 'auto'
    },

    turnCameraOff () {
      this.camera = 'off'
    },

    timeout (ms) {
      return new Promise(resolve => {
        window.setTimeout(resolve, ms)
      })
    }
  },
  computed: {
  validationPending () {
    return this.isValid === undefined
      && this.camera === 'off'
  },

  validationSuccess () {
    return this.isValid === true
  },

  validationFailure () {
    return this.isValid === false
  }
},
}
</script>
import { QrcodeStream } from 'vue3-qrcode-reader'

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
