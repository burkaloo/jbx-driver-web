<template>
  <div class="draw-container">
    <h3 class="m-0 text-center">Please Sign</h3>
    <canvas id="signature"></canvas>
    <div>
      <button class="btn btn-success canvas-btn" @click="save">Save</button>
      <button class="btn btn-warning canvas-btn" @click="clear">Clear</button>
      <button class="btn btn-danger canvas-btn" @click="cancel">Cancel</button>
    </div>
  </div>
</template>

<script>
import  SignaturePad  from 'signature_pad'
export default {
  name: 'drawpad',
  data(){
    return{
      pad: null,
      img: null,
      canvas: null
    }
  },
  mounted(){
    var el = document.getElementById("signature");
    this.canvas = el
    this.pad = new SignaturePad(el);
    window.addEventListener("resize", this.resizeCanvas);
    this.resizeCanvas();
  },
  beforeUnmout(){
    this.clear()
    this.canvas = null
    this.pad = null
    window.removeEventListener("resize", this.resizeCanvas);
  },
  methods:{
    resizeCanvas() {
      var ratio =  Math.max(window.devicePixelRatio || 1, 1);
      this.canvas.width = this.canvas.offsetWidth * ratio;
      this.canvas.height = this.canvas.offsetHeight * ratio;
      this.canvas.getContext("2d").scale(ratio, ratio);
      this.clear() // otherwise isEmpty() might return incorrect value
    },
    save(){
      if(!this.pad.isEmpty()){
        //var pic = this.pad.toDataURL("image/jpeg")
        this.img = this.pad.toDataURL()
        if(this.img !== null){
            this.$emit('img', this.img)
        }
      }
    },
    clear(){
      this.img = null
      this.pad.clear()
    },
    cancel(){
      this.clear()
      this.$emit('cancel')
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
canvas{
  width: 100%;
  height: calc(100vh - 70px)
}
.canvas-btn{
  width: 33.33%;
}
.draw-container{
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  width: 100vw;
  height: 100vh;
  background-color: white;
}
</style>
