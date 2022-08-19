<template>
  <div class="flex flex-col items-center p-4">
    <!-- Header -->
    <nav class="bg-gray-300 w-full md:w-3/4 p-4 rounded-lg shadow-sm flex">
      <vs-button @click="$router.go(-1)">
        <i class="bx bx-arrow-back mr-1"></i> Back
      </vs-button>
      <div class="flex flex-col justify-center ml-10">
        <h2>{{ movie.title }}</h2>
        <p class="text-sm hidden md:block">{{ movie.tagline }}</p>
      </div>
    </nav>

    <!-- Info -->
    <section v-if="!loading" class="mt-10 w-full md:w-3/4">
      <div class="flex flex-col md:flex-row gap-10">
        <!-- Image -->
        <div class="md:w-1/3 w-full rounded-lg overflow-hidden">
          <img
            :src="`https://image.tmdb.org/t/p/original${
              movie.poster_path || movie.backdrop_path
            }`"
            :alt="`${movie.title} poster`"
            class="object-cover w-full h-full"
          />
        </div>

        <!-- Details -->
        <div class="md:w-2/3 w-full h-fit flex flex-col gap-y-8 p-4">
          <div class="flex justify-between w-full">
            <strong>Budget</strong>
            <span>${{ priceSplitter(movie.budget) }}</span>
          </div>
          <div class="flex justify-between w-full">
            <strong>Revenue</strong>
            <span>${{ priceSplitter(movie.revenue) }}</span>
          </div>
          <div class="flex justify-between w-full">
            <strong>Release Date</strong>
            <span>{{ movie.release_date }}</span>
          </div>
          <div class="flex justify-between w-full">
            <strong>Runtime</strong>
            <span>{{ runtime(movie.runtime) }}</span>
          </div>
          <div class="flex justify-between w-full">
            <strong>Score</strong>
            <span>{{ movie.popularity }}</span>
          </div>
          <div class="flex justify-between w-full">
            <strong>Genres</strong>
            <span>{{ itemSplitter(movie.genres) }}</span>
          </div>
          <div class="flex justify-between w-full">
            <strong>IMDB Link</strong>
            <a
              :href="`https://www.imdb.com/title/${movie.imdb_id}/`"
              class="underline text-blue-600"
              target="_blank"
              >Link</a
            >
          </div>
          <div class="flex justify-between w-full">
            <strong>Homepage Link</strong>
            <a
              :href="movie.homepage"
              class="underline text-blue-600"
              target="_blank"
              >Link</a
            >
          </div>
        </div>
      </div>

      <!-- Description -->
      <p class="mt-10 shadow-lg p-4 rounded-md">{{ movie.overview }}</p>

      <!-- Credits -->
      <div class="flex flex-col mt-10">
        <strong>Credit:</strong>
        <p>{{ itemSplitter(movie.credits.cast.slice(0, 10)) }} ...</p>
      </div>
    </section>

    <!-- Loading -->
    <TheLoading v-show="loading" />
  </div>
</template>

<script>
import TheLoading from '../../components/UI/TheLoading.vue';
export default {
  name: 'MoivePage',
  components: { TheLoading },
  layout: 'MainLayout',
  asyncData({ params }) {
    const id = params.slug.split('-').at(-1);
    return { id };
  },
  data() {
    return {
      movie: {},
      loading: false,
    };
  },
  head() {
    return {
      title: this.movie.title,
    };
  },
  created() {
    this.getMovieDetail(this.id);
  },
  methods: {
    async getMovieDetail(id) {
      try {
        this.loading = true;
        const movieData = fetch(
          `${process.env.baseUrl}/movie/${id}?api_key=${process.env.apiKey}&language=en-US`
        );
        const movieCredits = fetch(
          `${process.env.baseUrl}/movie/${id}/credits?api_key=${process.env.apiKey}&language=en-US`
        );
        const [details, credits] = await Promise.all([movieData, movieCredits]);
        const [movie, creditsData] = await Promise.all([
          details.json(),
          credits.json(),
        ]);
        this.movie = movie;
        this.movie.credits = creditsData;
        this.loading = false;
      } catch (error) {
        this.loading = false;
        this.$vs.notification({
          title: 'Oops!',
          color: 'primary',
          progress: 'auto',
          text: 'Something went wrong. Please try again.',
        });
      }
    },
    priceSplitter(price = 0) {
      return price
        .toString()
        .match(/.{1,3}/g)
        .join(',');
    },
    runtime(time = 0) {
      if (time > 60) {
        return `${Math.floor(time / 60)}h ${time % 60}m`;
      }
      return `${time}m`;
    },
    itemSplitter(items = []) {
      return items.map((item) => item.name).join(', ');
    },
  },
};
</script>
