<template>
  <div class="app">
    <Nuxt keep-alive />
  </div>
</template>

<script>
export default {
  head() {
    return {
      title: 'Movie App',
    };
  },
  mounted() {
    this.$nextTick(() => {
      this.$nuxt.$loading.start();

      setTimeout(() => this.$nuxt.$loading.finish(), 500);
    });
  },
};
</script>
