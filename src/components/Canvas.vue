<template>
  <canvas v-bind:style='canvasStyle' @mousedown="mousedown" @mouseup="mouseup" @mousemove="mousemove" ref='canvas'></canvas>
</template>

<script>
export default {
  props: ['size'],
  computed: {
    canvasStyle: function () {
      return {
        'width': this.size + 'px',
        'height': this.size + 'px',
        'border': '1px solid #000',
        'margin': '4px',
      }
    },
  },
  methods: {
    drawImage(image) {
      this.ctx.drawImage(image, 0, 0);
    },
    clear() {
      this.ctx.fillStyle = 'rgb(255, 255, 255)';
      this.ctx.fillRect(0, 0, this.size, this.size);
    },
    image() {
      return this.ctx.getImageData(0, 0, this.size, this.size);
    },
    mousedown: function(event) {
      this.paint = true;
      this.ox = event.clientX - event.target.getBoundingClientRect().left;
      this.oy = event.clientY - event.target.getBoundingClientRect().top;
    },
    mouseup: function(event) {
      this.paint = false;
    },
    mousemove: function(event) {
      if (this.paint) {
        let x = event.clientX - event.target.getBoundingClientRect().left;
        let y = event.clientY - event.target.getBoundingClientRect().top;
        this.drawLine(this.ox, this.oy, x, y);
        this.ox = x;
        this.oy = y;
      }
    },
    drawLine: function(ox, oy, x, y) {
      this.ctx.beginPath();
      this.ctx.moveTo(ox, oy);
      this.ctx.lineTo(x, y);
      this.ctx.stroke();
    }
  },
  mounted() {
    this.ctx = this.$el.getContext('2d')
    this.ox = 0;
    this.oy = 0;
    this.$refs.canvas.width = this.size;
    this.$refs.canvas.height = this.size;
  }
}
</script>

<style scoped>
</style>