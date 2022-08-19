<template>
  <vs-card>
    <template #title>
      <h3>
        {{ movie.title.slice(0, 25) }}
        {{ movie.title.length > 25 ? '...' : '' }}
      </h3>
    </template>
    <template #img>
      <img
        :src="`https://image.tmdb.org/t/p/original${
          movie.backdrop_path || movie.poster_path
        }`"
        :alt="`${movie.title} poster`"
        class="max-h-48 object-cover"
      />
    </template>
    <template #text>
      <p>{{ movie.release_date }}</p>
    </template>
    <template #interactions>
      <vs-button danger icon>
        <i class="bx bx-heart"></i>
      </vs-button>
      <vs-button class="btn-chat" shadow primary>
        <i class="bx bx-chat"></i>
        <span class="span"> 54 </span>
      </vs-button>
    </template>
  </vs-card>
</template>

<script>
export default {
  props: {
    movie: {
      type: Object,
      required: true,
    },
  },
};
</script>
