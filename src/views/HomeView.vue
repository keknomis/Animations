<script setup>
  import {onMounted} from "vue";

  let canvas = null
  let ctx = null

  let width = null
  let height = null

  function random(min, max) {
      return Math.floor(Math.random() * (max - min + 1)) + min;
  }

  function randomRGB() {
      return `rgb(${random(0, 255)},${random(0, 255)},${random(0, 255)})`;
  }

  class Ball {

      constructor(x, y, velX, velY, color, size) {
          this.x = x;
          this.y = y;
          this.velX = velX;
          this.velY = velY;
          this.color = color;
          this.size = size;
      }

      draw() {
          ctx.beginPath();
          ctx.fillStyle = this.color;
          ctx.arc(this.x, this.y, this.size, 0, 2 * Math.PI);
          ctx.fill();
      }

      update() {
          if ((this.x + this.size) >= width) {
              this.velX = -(Math.abs(this.velX));
          }

          if ((this.x - this.size) <= 0) {
              this.velX = Math.abs(this.velX);
          }

          if ((this.y + this.size) >= height) {
              this.velY = -(Math.abs(this.velY));
          }

          if ((this.y - this.size) <= 0) {
              this.velY = Math.abs(this.velY);
          }

          this.x += this.velX;
          this.y += this.velY;
      }

      collisionDetect() {
          for (const ball of balls) {
              if (!(this === ball)) {
                  const dx = this.x - ball.x;
                  const dy = this.y - ball.y;
                  const distance = Math.sqrt(dx * dx + dy * dy);

                  if (distance < this.size + ball.size) {
                      ball.color = this.color = randomRGB();
                  }
              }
          }
      }

  }

  const balls = [];

  while (balls.length < 25) {
      const size = random(10,20);
      const ball = new Ball(
          // ball position always drawn at least one ball width
          // away from the edge of the canvas, to avoid drawing errors
          random(0 + size,width - size),
          random(0 + size,height - size),
          random(-7,7),
          random(-7,7),
          randomRGB(),
          size
      );

      balls.push(ball);
  }

  function loop() {
      ctx.fillStyle = 'rgba(0, 0, 0, 0.25)';
      ctx.fillRect(0, 0,  width, height);

      for (const ball of balls) {
          ball.draw();
          ball.update();
          ball.collisionDetect();
      }

      requestAnimationFrame(loop);
  }



  onMounted(()=>{
      canvas = document.querySelector("canvas")
      ctx = canvas.getContext("2d")
       width = canvas.width = window.innerWidth;
       height = canvas.height = window.innerHeight;
      loop();

  })

</script>

<template>
    <h1>Bounce</h1>
    <canvas></canvas>
</template>

<style scoped>
html, body{
    margin:0;
}
html{
    font-family: 'Helvetica Neu', Helvetica, Arial, sans-serif;
    height: 100%;
}
body{
    overflow:hidden;
    height: inherit;
}
h1 {
    font-size: 2rem;
    letter-spacing: -1px;
    position: absolute;
    margin: 0;
    top: -4px;
    right: 5px;

    color: white;
    text-shadow: 0 0 4px white;
}

</style>