<template>
  <table class="rpl-search-results-table">
    <thead>
      <tr>
        <th v-for="col in columns" :colspan="col.colspan" :key="`th-${col.key}`">{{col.label}}</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(row, rowIdx) in rows" :key="`row-${rowIdx}`">
        <td :colspan="col.colspan || 1" v-for="(col, colIdx) in row" :key="`col-${colIdx}-row-${rowIdx}`">
          <div :is="col.component" v-bind="col.data">{{col.data}}</div>
        </td>
      </tr>
    </tbody>
  </table>
</template>

<script>

export default {
  name: 'rpl-search-results-table',
  props: {
    items: Array,
    columnConfig: {
      type: Array,
      default: function () {
        return []
      }
    }
  },
  computed: {
    columns () {
      if (!this.columnConfig || this.columnConfig.length === 0) {
        const firstCol = [...new Set(this.items.map(item => Object.keys(item)))][0]
        return firstCol.map(val => {
          return {
            key: val,
            label: val.charAt(0).toUpperCase() + val.slice(1),
            colspan: 1
          }
        })
      }
      return this.columnConfig
    },
    rows () {
      if (this.items) {
        return this.items.map(item => {
          return this.columns.map(config => {
            const col = item[config.key]
            return {
              component: config ? config.component || 'span' : 'span',
              data: col,
              ...config
            }
          })
        })
      }
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
