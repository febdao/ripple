<template>
  <table class="rpl-search-results-table">
    <thead>
      <tr>
        <th v-for="(th, index) in thead" :key="`th-${index}`">{{th}}</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(row, rowIdx) in items" :key="`row-${rowIdx}`">
        <td v-for="(col, colIdx) in row" :key="`col-${colIdx}-row-${rowIdx}`">{{col}}</td>
      </tr>
    </tbody>
  </table>
</template>

<script>
import { keys } from 'lodash'
export default {
  name: 'rpl-search-results-table',
  props: {
    items: Array
  },
  computed: {
    thead () {
      return [...new Set(this.items.map(item => Object.keys(item)))][0]
    }
  }
}
</script>

<style lang="scss">
@import "~@dpc-sdp/ripple-global/style";
$table-stripe-color: rpl-color('light_neutral');
$table-border: 1px solid rpl-color('mid_neutral_1');
$table-header-ruleset: ('s', 1em, 'bold');
$table-padding: $rpl-space-4;

  .rpl-search-results-table {
    border: $table-border;
    border-radius: rem(4px);
    overflow: auto;
    width: 100%;
    -webkit-overflow-scrolling: touch; // sass-lint:disable-line no-vendor-prefixes

    table {
      border-collapse: collapse;
      width: 100%;

      caption {
        text-align: left;
        padding: $table-padding;
        vertical-align: top;
      }

      thead {
        tr {
          background-color: $table-stripe-color;
        }
      }

      tbody {
        tr {
          &:nth-child(even) {
            background-color: $table-stripe-color;
          }
        }
      }

      th {
        @include rpl_typography_ruleset($table-header-ruleset);
        text-align: left;
      }

      th,
      td {
        padding: $table-padding;
        vertical-align: top;
      }
    }
  }
</style>
