<template>
  <div class="error">
    <p>
      {{msg}}
    </p>
  </div>
</template>

<script>
export default {
  name: 'Error',
  props: {
    msg: String,
  },
};

</script>

<style lang="less">
  .error {
    width: 100%;
    display: flex;
    justify-content: center;
    align-content: center;
    font-size: 24px;
    text-align: center;
  }
</style>
