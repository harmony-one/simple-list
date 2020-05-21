<style scoped lang="less">
@import '../less/common.less';
</style>

<template>
  <div class="address-page explorer-page page">
    <div class="address-body explorer-body">
      <div v-if="!loading" class="container">
        <div class="explorer-card">
          <header>
            <h1>ONE Holders</h1>
          </header>

          <div class="explorer-card-body">
            <TransactionsTable
              :all-txs="allTxs"
              with-shards="true"
              :page="page"
              :changePage="changePage"
            >
            </TransactionsTable>
          </div>
        </div>
      </div>
      <div v-else class="container">
        <loading-message />
      </div>
    </div>
  </div>
</template>

<script>
import LoadingMessage from './LoadingMessage';
import TransactionsTable from './TransactionsTable';
import axios from 'axios';

export default {
  name: 'AddressPage',
  components: {
    LoadingMessage,
    TransactionsTable,
  },
  data() {
    return {
      loading: true,
      allTxs: [],
    };
  },
  computed: {
    txCount() {
      return this.allTxs.length;
    },
    page() {
      return this.$route.query.page - 1 || 0;
    },
  },
  watch: {
    $route() {
      // if (this.$route.params.address !== (this.address && this.address.id)) {
      //   this.getAddress();
      // }
    },
  },
  mounted() {
    this.getData();
  },
  methods: {
    changePage(value) {
      this.$router.replace({
        name: 'HomePage',
        query: { page: value + 1, txType: this.$route.query.txType },
      });
    },
    getData() {
      axios
        .get('https://harmony-explorer-mainnet.firebaseio.com/one-holder.json')
        .then(response => {
          const { data } = response;
          const resData = [];
          let i = 0;
          while (data.address[i] !== undefined) {
            resData.push({
              address: data.address[i],
              balance: data.balance[i],
              transactions: data['transaction-count'][i],
            });
            i++;
          }
          this.allTxs = resData;
          this.loading = false;
        });
    },
  },
};
</script>
