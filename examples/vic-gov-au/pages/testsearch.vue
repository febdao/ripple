<template>
  <rpl-page-layout class="app-main" :sidebar="sidebar">

    <template slot="breadcrumbs" v-if="breadcrumbs">
      <rpl-breadcrumbs :crumbs="breadcrumbs" />
    </template>

    <template slot="aboveContent" >
      <rpl-search-form
        @search="getSearchResults(searchForm.filterForm.model.title || '')"
        class="rpl-site-constrain--on-all"
        v-bind="searchForm"
      />
      <rpl-divider />
    </template>
    <transition name="fade">
      <rpl-search-results-layout
        :searchResults="searchResults"
        :pager="searchResults.length === 0 ? undefined : pager"
        :responseSize="searchResults.length === 0 ? 0 : searchOptions.responseSize"
        :count="searchResults.length === 0 ? 0 : count"
        :errorMsg="errorMsg"
        :noResultsMsg="noResultsCopy"
        @pager-change="changed"
        v-if="!loading"
      >
        <template slot="sort">
          <rpl-search-sort @search-sort-change="changeSort" :options="sortOptions" />
        </template>
        <template v-slot:results="resultsProps">
          <rpl-search-results-table :items="resultsProps.searchResults" :columnConfig="columns" />
        </template>
      </rpl-search-results-layout>
    </transition>
  </rpl-page-layout>
</template>

<script>
import { RplDivider } from '@dpc-sdp/ripple-global'
import RplBreadcrumbs from '@dpc-sdp/ripple-breadcrumbs'
import { RplRow, RplCol } from '@dpc-sdp/ripple-grid'
import { RplPageLayout } from '@dpc-sdp/ripple-layout'
import { breadcrumbs as getBreadcrumbs } from '@dpc-sdp/ripple-nuxt-tide/lib/core/breadcrumbs'

import { RplSearchForm, RplSearchResults, RplSearchResultsLayout, RplSearchResultsTable, RplSearchSort } from '@dpc-sdp/ripple-search'
import formData from './formdata.js'
import { searchMixin } from '@dpc-sdp/ripple-nuxt-tide/modules/search'

export default {
  name: 'ExampleSearch',
  components: {
    RplDivider,
    RplBreadcrumbs,
    RplSearchForm,
    RplSearchSort,
    RplSearchResults,
    RplSearchResultsTable,
    RplSearchResultsLayout,

    // Layout.
    RplPageLayout,
    RplRow,
    RplCol
  },
  mixins: [searchMixin],
  async asyncData ({ app, route }) {
    const searchForm = await formData.getFormData(app.$tideSearch.setFilterOptions)
    return {
      sidebar: false,
      breadcrumbs: getBreadcrumbs(route.path, searchForm.title, null),
      searchComponent: 'RplCardProfile',
      searchForm,
      searchOptions: {
        currentSiteOnly: true,
        defaultHits: false,
        responseSize: 9,
        qFields: ['title'],
        sFields: [
          'field_profile_category_name',
          'field_profile_expertise_name',
          'field_life_span',
          'field_media_image_absolute_path',
          'field_year',
          'summary_processed',
          'field_paragraph_summary',
          'field_landing_page_summary',
          'field_profile_intro_text',
          'field_location_name',
          'title',
          'url'
        ]
      },
      sort: false,
      docType: 'profile'
    }
  },
  data () {
    return {
      columns: [
        {
          label: 'Title',
          component: () => import(/* webpackChunkName: 'rpl-text-link' */ '@dpc-sdp/ripple-link').then(m => m.RplTextLink),
          colspan: 2,
          key: 'title'
        },
        {
          label: 'Status',
          component: () => import(/* webpackChunkName: 'ripple-meta-tag' */ '@dpc-sdp/ripple-meta-tag'),
          colspan: 1,
          key: 'status'
        },
        {
          label: 'Year',
          colspan: 1,
          key: 'year'
        }
      ],
      sortOptions: [
        {
          value: { field: 'title.keyword', order: 'asc' },
          label: 'Title - Ascending'
        },
        {
          value: { field: 'title.keyword', order: 'desc' },
          label: 'Title - Descending'
        }
      ]
    }
  },
  methods: {
    changeSort (val) {
      console.log('val', val)
      this.sort = val
      this.getSearchResults()
    },
    getComputedFilters () {
      // Remove title from filter terms as it is being sent as the search query
      const filters = this.$tideSearch.getFiltersValues(this.searchForm.filterForm)
      if (filters.title) {
        delete filters.title
      }
      return filters
    },
    mapSearchResults (source) {
      const titleLimit = 80
      const lifespanLimit = 50
      const summaryLimit = 100
      return {
        title: this.getLink(source.url, this.$store.state.tide.siteData.drupal_internal__tid, source.field_node_primary_site, this.$store.state.tideSite.sitesDomainMap, { text: 'text', url: 'url' }, this.truncateText(source.title[0], titleLimit)),
        year: source.field_year ? source.field_year[0] : '',
        status: {
          linkText: source.field_profile_category_name ? source.field_profile_category_name[0] : ''
        },
        lifespan: source.field_life_span ? this.truncateText(source.field_life_span[0], lifespanLimit) : '',
        summary: typeof source.summary_processed !== 'undefined' && source.summary_processed[0].length > 1 ? this.truncateText(source.summary_processed[0], summaryLimit) : this.truncateText(source.field_landing_page_summary[0], summaryLimit),
        image: source.field_media_image_absolute_path ? source.field_media_image_absolute_path[0] : ''
      }
    }
  }
}
</script>

<style lang="scss" scoped>
  .fade-enter-active, .fade-leave-active {
    transition: opacity .5s;
  }
  .fade-enter, .fade-leave-to {
    opacity: 0;
  }
</style>
