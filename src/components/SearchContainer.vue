<template>
  <section :class="classes">
    <search-field class="field" @update="search($event)" />
    <search-results :results="results" />
  </section>
</template>

<script>
  import getServices from '@/services';
  import search, { createIndex } from '@/search';
  import SearchField from '@/components/SearchField';
  import SearchResults from '@/components/SearchResults';

  export default {
    components: { SearchField, SearchResults },
    data () {
      return {
        index: null,
        results: [],
        services: [],
        isLoading: true,
      };
    },
    computed: {
      classes () {
        return [
          'search-container', {
            '-is-loading': this.isLoading,
          }
        ];
      },
    },
    methods: {
      search (term) {
        this.results = search(term, this.index, this.services);
      },
    },
    async mounted () {
      this.services = await getServices();
      this.index = createIndex(this.services);
      this.isLoading = false;
    },
  }
</script>

<style lang="stylus">
  .search-container
    &
      box-sizing: border-box
      display: flex
      align-items: center
      flex-direction: column
      justify-content: center
      min-height: 100vh
      padding: 16px 0

    @media screen and (min-width: 480px)
      padding: 16px 16px

    > .field
      margin-bottom: 16px

    &::before
      position: fixed
      bottom: 0
      z-index: -1
      display: block
      width: 100%
      height: 100%
      background-position: top right
      background-repeat: no-repeat
      background-image: url('~@/assets/images/background.jpg')
      background-size: cover
      transition: filter .3s ease
      content: ''

    &.-is-loading::before
      filter: blur(5px)
</style>
