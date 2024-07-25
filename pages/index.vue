<template>
  <div
    :class="[
      'flex flex-col items-center',
      fullScreen ? 'w-screen h-screen justify-center' : 'w-full min-h-screen',
    ]"
  >
    <img src="/search-logo.png" alt="search" class="w-1/4" />
    <div class="w-1/2">
      <input
        type="text"
        v-model="query"
        @keyup.enter="search"
        placeholder="Search..."
        class="w-full p-4 rounded-3xl"
      />
    </div>

    <div class="w-1/2">
      <Card v-for="result in result" :key="result.pageid" :result="result" />
      <div v-if="isLoading" class="p-4 text-center text-white">Loading...</div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import Card from '~/components/card.vue';

export default {
  data() {
    return {
      query: '',
      result: [],
      page: 1,
      isLoading: false,
    };
  },
  components: {
    Card,
  },
  computed: {
    fullScreen() {
      return this.result.length === 0;
    },
  },
  methods: {
    async search() {
      if (this.query) {
        this.page = 1;
        this.result = [];
        await this.fetchData();
      }
    },
    async fetchData() {
      if (this.query && !this.isLoading) {
        this.isLoading = true;
        try {
          const response = await axios.get(
            `https://en.wikipedia.org/w/api.php`,
            {
              params: {
                action: 'query',
                list: 'search',
                prop: 'info',
                inprop: 'url',
                utf8: '',
                format: 'json',
                origin: '*',
                srlimit: 20,
                srsearch: this.query,
                sroffset: (this.page - 1) * 20,
              },
            }
          );
          this.result = [...this.result, ...response.data.query.search];
          this.page++;
          console.log(this.result);
        } catch (error) {
          console.log('error at the time of fetching data from api', error);
        } finally {
          this.isLoading = false;
        }
      }
    },
    onScroll() {
      const bottomWindow =
        window.innerHeight + window.scrollY >=
        document.documentElement.offsetHeight - 10;
      if (bottomWindow && !this.isLoading) {
        this.fetchData();
      }
    },
  },
  mounted() {
    window.addEventListener('scroll', this.onScroll);
  },
  beforeDestroy() {
    window.removeEventListener('scroll', this.onScroll);
  },
};
</script>
