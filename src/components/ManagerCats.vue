<template>
  <div class="manager-cats">
    <div class="manager-cats__el manager-cats__el--portrait">
      <button class="manager-cats__btn">
        <router-link to="/cat-mono">Выводи по одному!</router-link>
      </button>
    </div>
    <div class="manager-cats__el manager-cats__el--albums">
      <button class="manager-cats__btn">
        <router-link to="/cat-list">А теперь все сразу</router-link>
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ManagerCats',
};
</script>

<style lang="less">
  .manager-cats {
    display: flex;
  }
  .manager-cats__el {
    width: 50%;
    height: 100vh;
    display: flex;
    align-content: center;
    align-items: center;
    justify-content: center;
    background-size: cover;
    background-position: 0 0;
    background-repeat: no-repeat;

    &--portrait {
      background-image: linear-gradient(
          rgba(255, 255, 255, 0.8),
          rgba(0, 0, 0, 0.5)), url("../assets/portrait.jpg");
    }
    &--albums {
      background-image: linear-gradient(
          rgba(255, 255, 255, 0.8),
          rgba(0, 0, 0, 0.5)), url("../assets/albums.jpg");
    }
  }
  .manager-cats__btn {
    width: max-content;
    position: relative;
    top: 50px;
  }

  @media (max-width: 320px) {
    .manager-cats {
      flex-wrap: wrap;
    }
    .manager-cats__el {
      width: 100%;
    }
    .manager-cats__btn {
      top: 0;
      width: 200px;
      padding: 10px;
    }
  }
</style>
