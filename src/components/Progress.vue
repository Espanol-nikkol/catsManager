<template>
  <div class="download">
    <span class="download__text">Надо немного подождать</span>
    <div class="download__bar"></div>
  </div>
</template>

<script>
import { Circle } from 'progressbar.js';

export default {
  name: 'Progress',
  mounted() {
    const barContainer = document.querySelector('.download__bar');
    const bar = new Circle(barContainer, {
      color: '#FFEA82',
      trailColor: '#eee',
      trailWidth: 1,
      duration: 2800,
      easing: 'bounce',
      strokeWidth: 6,
      from: {
        color: '#FFEA82',
        a: 0,
      },
      to: {
        color: '#6fc841',
        a: 1,
      },
      step: (state, circle) => {
        circle.path.setAttribute('stroke', state.color);
      },
    });
    bar.animate(1.0);
  },
};
</script>

<style lang="less">
  .download {
    width: 100%;
    height: 50vh;
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    align-content: center;
  }

  .download__text {
    width: 100%;
    margin-bottom: 20px;
    text-align: center;
  }

  .download__bar {
    width: 200px;
    height: 200px;
  }
</style>
