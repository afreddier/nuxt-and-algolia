<template>
  <div class="container py-20 m-auto">
    <ais-instant-search-ssr>
      <div class="mb-10">
        <div class="flex justify-between items-end mb-6">
          <h1 class="text-2xl text-gray-700 leading-tight">
            Search
          </h1>

          <client-only>
            <ais-powered-by />
          </client-only>
        </div>
      </div>

      <ais-search-box 
        placeholder="e.g. Macbook Pro"
        :class-names="{
          'ais-SearchBox' : 'w-full',
          'ais-SearchBox-input': 'border-2 border-gray-400 rounded block w-full p-4 text-lg',
          'ais-SearchBox-submit': 'hidden',
          'ais-SearchBox-reset': 'hidden',
        }"
      />

      <ais-state-results>
        <div slot-scope="{ query }">
          <div v-if="query.length">
            <ais-stats>
              <h1 
                class="text-md mb-10 text-gray-700"
                slot-scope="{ nbHits }">
                Found {{ nbHits }} results
              </h1>
            </ais-stats>

            <ais-pagination 
              :class-names="aisPaginationClassNames"
            />

            <ais-hits
              :class-names="{
                'ais-Hits': 'mb-10'
              }"
            >
              <div slot="item" slot-scope="{ item }">
                <SomeComponent :item="item" />
              </div>
            </ais-hits>

            <ais-pagination 
              :class-names="aisPaginationClassNames"
            />

          </div>
        </div>
      </ais-state-results>

    </ais-instant-search-ssr>
  </div>
</template>

<script>
  import {
    AisInstantSearchSsr,
    AisHits,
    AisSearchBox,
    AisStateResults,
    AisStats,
    AisPoweredBy,
    AisPagination
  } from 'vue-instantsearch'
  
  import SomeComponent from '@/components/SomeComponent'

export default {
  components: {
    AisInstantSearchSsr,
    AisHits,
    AisSearchBox,
    AisStateResults,
    AisStats,
    AisPoweredBy,
    AisPagination,

    SomeComponent
  },

  data () {
    return {
      instantSearchState: null,

      aisPaginationClassNames: {
        'ais-Pagination': 'mb-10',
        'ais-Pagination-list': 'flex flex-wrap',
        'ais-Pagination-link': 'inline-block w-10 h-10 flex items-center justify-center',
        'ais-Pagination-item': 'bg-white rounded mr-3 mb-2',
        'ais-Pagination-item--selected': 'bg-blue-500 text-white'
      }
    }
  },

  provide () {
    return {
      $_ais: this.$instantsearch
    }
  },

  beforeMount () {
    this.$instantsearch.hydrate(this.instantSearchState)
  },

  asyncData ({ app, query }) {
    const instantsearch = app.$instantsearch

    return instantsearch
      .findResultsState({
        query: query.query,
        hitsPerPage: 10
      })
      .then(() => {
        return {
          instantSearchState: instantsearch.getState()
          }
      })
  }
}
</script>
