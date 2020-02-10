<template>
  <div class="home">
    <p>PIX2PIX Demo</p>
    <Canvas size=256 ref="canvas"/>
    <Canvas size=256 ref="transfer"/>
    <div id="buttonContainer">
      <button v-on:click="clear">Clear</button>
      <button v-on:click="reset">Reset</button>
      <button v-on:click="transfer" id="transferButton">Transfer</button>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import Canvas from '@/components/Canvas.vue'
import ml5 from 'ml5'

export default {
  name: 'home',
  components: {
    Canvas,
  },
  methods: {
    modelLoaded: function() {
      console.log('model loaded');

      this.transfer();
    },
    clear() {
      console.log('clear');

      let canvas = this.$refs.canvas;
      canvas.clear();

      let transfer = this.$refs.transfer;
      transfer.clear();
    },
    reset() {
      let img = new Image();
      img.src = require('@/assets/xxx.png');
      img.addEventListener('load', function() {
        this.$refs.canvas.drawImage(img);
      }.bind(this));
    },
    transfer() {
      const SIZE = 256;

      console.log('transfer');

      let isTransfering = true;
      let canvasElement = this.$refs.canvas.$el;

      this.pix2pix.transfer(canvasElement, function(err, result) {
        if (err) {
          console.log(err);
        }
        if (result && result.src) {
          isTransfering = false;
          let img = new Image();
          img.src = result.src; 
          img.addEventListener("load", function() {
            this.$refs.transfer.drawImage(img);
          }.bind(this));

        }
      }.bind(this));
    }
  },
  mounted() {
    console.log('mounted');

    this.reset();
    this.pix2pix = ml5.pix2pix('edges2cats_AtoB.pict', this.modelLoaded);
  },
}
</script>
