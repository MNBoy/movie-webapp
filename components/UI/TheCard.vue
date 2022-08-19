<template>
  <NuxtLink :to="`/movie/${slugGen(movie.title)}-${movie.id}`">
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
        <p>{{ findGenres(movie.genre_ids) }}</p>
      </template>
      <template #interactions>
        <vs-button danger icon>
          <i class="bx bx-heart mr-1"></i>
          <span class="span">{{ movie.vote_average }}</span>
        </vs-button>
        <vs-button class="btn-chat" shadow primary>
          <i class="bx bx-star mr-1"></i>
          <span class="span">{{ movie.vote_count }}</span>
        </vs-button>
      </template>
    </vs-card>
  </NuxtLink>
</template>

<script>
export default {
  props: {
    movie: {
      type: Object,
      required: true,
    },
    genres: {
      type: Array,
      required: true,
    },
  },
  methods: {
    slugGen(title) {
      return title
        .replace(/[^\w\s]/gi, '')
        .replace(/ /g, '-')
        .toLowerCase();
    },
    findGenres(idList) {
      return idList
        .map((id) => {
          return this.genres.find((genre) => genre.id === id);
        })
        .map((genre) => genre.name)
        .join(', ');
    },
  },
};
</script>
