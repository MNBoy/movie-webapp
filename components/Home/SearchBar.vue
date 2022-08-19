<template>
  <div class="flex flex-col md:flex-row gap-x-5 gap-y-10 justify-center">
    <vs-input v-model="startDate" primary type="date" label="Start date" />
    <vs-input v-model="endDate" primary type="date" label="End date" />
    <vs-button @click="search">Search</vs-button>
  </div>
</template>

<script>
export default {
  props: {
    getMovies: {
      type: Function,
      required: true,
    },
  },
  data() {
    return {
      startDate: null,
      endDate: null,
    };
  },
  methods: {
    search() {
      this.getMovies(1, this.startDate, this.endDate, true);
    },
  },
};
</script>
