<style scoped lang="less">
@import '../less/common.less';
</style>

<template>
  <div class="transactions-table explorer-card">
    <header>
      <slot></slot>
      <div class="pagination-controls">
        <span class="total-tx-num">{{ txCount }} total </span>
        <span class="page-controllers">
          <span class="page-navigator">
            <button
              class="btn btn-light btn-icon-only"
              :disabled="pageIndex === 0"
              @click="first()"
            >
              <font-awesome-icon icon="angle-double-left" />
            </button>
            <button
              class="btn btn-light btn-icon-only"
              :disabled="pageIndex === 0"
              @click="prev()"
            >
              <font-awesome-icon icon="chevron-left" />
            </button>
            <span class="pagination-nums">
              {{ pageIndex + 1 }} / {{ pageCount }}
            </span>
            <button
              class="btn btn-light btn-icon-only"
              :disabled="pageIndex === pageCount - 1"
              @click="next()"
            >
              <font-awesome-icon icon="chevron-right" />
            </button>
            <button
              class="btn btn-light btn-icon-only"
              :disabled="pageIndex === pageCount - 1"
              @click="last()"
            >
              <font-awesome-icon icon="angle-double-right" />
            </button>
          </span>
        </span>
      </div>
    </header>
    <div class="explorer-card-body">
      <section>
        <table class="explorer-table">
          <tr>
            <th>Address</th>
            <th class="text-right">Balance</th>
            <th class="text-right">Transactions</th>
          </tr>
          <tr v-for="tx in txs" :key="tx.hash">
            <td class="address_link">
              <a :href="'https://explorer.harmony.one/#/address/' + tx.address">
                {{ tx.address }}
              </a>
            </td>
            <td class="text-right">
              {{ tx.balance }}
            </td>
            <td class="text-right">
              {{ tx.transactions | number }}
            </td>
          </tr>
        </table>
      </section>
    </div>
  </div>
</template>

<script>
export default {
  name: 'TransactionsTable',
  props: ['allTxs', 'withShards', 'page', 'changePage'],
  data() {
    return {
      loading: true,
      pageIndex: this.page || 0,
      pageSize: 20,
    };
  },
  computed: {
    txCount() {
      return this.allTxs.length;
    },
    pageCount() {
      return Math.ceil(this.txCount / this.pageSize);
    },
    txs() {
      const begin = this.pageIndex * this.pageSize;

      return this.allTxs.slice(begin, begin + this.pageSize);
    },
  },
  watch: {
    allTxs() {
      this.pageIndex = 0;
    },
  },
  methods: {
    goToPage(index) {
      if (index < 0) index = 0;
      if (index >= this.pageCount) index = this.pageCount - 1;

      this.pageIndex = index;

      if (this.changePage) {
        this.changePage(index);
      }
    },
    first() {
      this.goToPage(0);
    },
    last() {
      this.goToPage(this.pageCount - 1);
    },
    prev() {
      if (this.pageIndex === 0) return;
      this.goToPage(this.pageIndex - 1);
    },
    next() {
      if (this.pageIndex === this.pageCount - 1) return;
      this.goToPage(this.pageIndex + 1);
    },
  },
};
</script>
