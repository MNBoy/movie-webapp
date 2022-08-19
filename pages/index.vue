<template>
  <div class="flex justify-center p-4 flex-col relative">
    <!-- Search Bar -->
    <SearchBar class="mt-10" :get-movies="getMovies" />

    <!-- Cards -->
    <div
      v-if="moviesPage.length && !loading"
      class="mt-10 grid grid-cols-1 sm:grid-cols-2 md:grid-cols-4 gap-4 place-content-center place-self-center"
    >
      <TheCard
        v-for="movie in moviesPage"
        :key="movie.id"
        :movie="movie"
        :genres="genres"
      />
    </div>

    <!-- Alert -->
    <vs-alert v-if="moviesPage.length < 1 && !loading" shadow class="mt-10">
      <template #title> Oops! </template>
      <b>No movies found.</b> Please select different dates.
    </vs-alert>

    <!-- Pagination -->
    <div class="mt-10 flex flex-col gap-y-2 justify-center items-center">
      <div class="flex gap-x-4">
        <vs-pagination v-model="page" only-arrows :length="10" />
        <span>
          Page: <b>{{ page }}</b>
        </span>
      </div>
      <span>Showing results {{ showingResults }}</span>
    </div>

    <!-- Loading -->
    <TheLoading v-show="loading" />
  </div>
</template>

<script>
import SearchBar from '../components/Home/SearchBar.vue';
import TheCard from '../components/UI/TheCard.vue';
import TheLoading from '../components/UI/TheLoading.vue';

export default {
  name: 'HomePage',
  components: {
    SearchBar,
    TheCard,
    TheLoading,
  },
  layout: 'MainLayout',
  data() {
    return {
      page: 1,
      cachedPages: [],
      movies: [],
      moviesPage: [],
      totalPages: 0,
      totalResults: 0,
      loading: false,
      startDate: null,
      endDate: null,
      genres: [],
    };
  },
  computed: {
    showingResults() {
      return `${this.page * 20 - 19} - ${this.page * 20}`;
    },
  },
  watch: {
    page(newPage) {
      this.getMovies(newPage);
    },
  },
  created() {
    this.getGenres();
    this.getMovies();
  },
  methods: {
    async getMovies(
      page = 1,
      startDate = this.startDate,
      endDate = this.endDate,
      searchByDate = false
    ) {
      this.loading = true;

      if (searchByDate) {
        this.movies = [];
        this.cachedPages = [];
        this.startDate = startDate;
        this.endDate = endDate;
        if (this.page !== 1) {
          this.page = 1;
          return;
        }
      }
      // If page is cached, get it from cache
      if (this.cachedPages.includes(page) && !searchByDate) {
        this.moviesPage = this.movies.slice((page - 1) * 20, page * 20);
        this.loading = false;
        return;
      }

      // Get movies from API
      try {
        const response = await fetch(
          `${process.env.baseUrl}/discover/movie?api_key=${
            process.env.apiKey
          }&language=en-US&sort_by=popularity.desc&include_adult=false&include_video=false&page=${page}&with_watch_monetization_types=flatrate${
            startDate ? `&primary_release_date.gte=${startDate}` : ''
          }${endDate ? `&primary_release_date.lte=${endDate}` : ''}`
        );
        const data = await response.json();
        this.movies = [...this.movies, ...data.results];
        this.moviesPage = this.movies.slice((page - 1) * 20, page * 20);
        this.totalPages = data.total_pages;
        this.totalResults = data.total_results;
      } catch (error) {
        this.$vs.notification({
          title: 'Oops!',
          color: 'primary',
          progress: 'auto',
          text: 'Something went wrong. Please try again.',
        });
      }

      // If page not cached, get it
      if (!this.cachedPages.includes(page)) {
        this.cachedPages.push(page);
      }

      this.loading = false;
    },
    async getGenres() {
      try {
        const response = await fetch(
          `${process.env.baseUrl}/genre/movie/list?api_key=${process.env.apiKey}&language=en-US`
        );
        const data = await response.json();
        this.genres = data.genres;
      } catch (error) {
        this.$vs.notification({
          title: 'Oops!',
          color: 'primary',
          progress: 'auto',
          text: 'Something went wrong. Please try again.',
        });
      }
    },
  },
};
</script>
