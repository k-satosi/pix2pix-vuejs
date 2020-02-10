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
import Canvas from '@/components/Canvas.vue';
import ml5 from 'ml5';

export default {
  name: 'home',
  components: {
    Canvas,
  },
  methods: {
    modelLoaded() {
      console.log('model loaded');

      this.transfer();
    },
    clear() {
      console.log('clear');

      const { canvas } = this.$refs;
      canvas.clear();

      const { transfer } = this.$refs;
      transfer.clear();
    },
    reset() {
      const img = new Image();
      // eslint-disable-next-line global-require
      img.src = require('@/assets/cat.png');
      img.addEventListener('load', () => {
        this.$refs.canvas.drawImage(img);
      });
    },
    transfer() {
      const SIZE = 256;

      console.log('transfer');

      let isTransfering = true;
      const canvasElement = this.$refs.canvas.$el;

      this.pix2pix.transfer(canvasElement, (err, result) => {
        if (err) {
          console.log(err);
        }
        if (result && result.src) {
          isTransfering = false;
          const img = new Image();
          img.src = result.src;
          img.addEventListener('load', () => {
            this.$refs.transfer.drawImage(img);
          });
        }
      });
    },
  },
  mounted() {
    console.log('mounted');

    this.reset();
    this.pix2pix = ml5.pix2pix('edges2cats_AtoB.pict', this.modelLoaded);
  },
};
</script>
