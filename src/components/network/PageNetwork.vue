<template>
  <TmPage
    :managed="true"
    :loading="!lastHeader"
    :loaded="!!lastHeader"
    :error="blocks.error"
    data-title="Network"
  >
    <template slot="managed-body">
      <div class="page-profile__header page-profile__section network">
        <div class="row">
          <div class="page-profile__header__info">
            <div class="page-profile__status-and-title">
              <span
                v-tooltip.top="status.message"
                :class="status.color"
                class="page-profile__status"
              />
              <h2 class="page-profile__title">
                {{ lastHeader.chain_id || `Not connected to a node` }}
              </h2>
            </div>
          </div>
        </div>
        <div class="row">
          <dl class="col-lg-4 col-md-4 col-sm-6 col-xs-12 info_dl">
            <dt>BLOCK HEIGHT</dt>
            <dd>
              {{
                lastHeader.height
                  ? `#` + num.prettyInt(lastHeader.height)
                  : `--`
              }}
            </dd>
          </dl>
          <dl class="col-lg-4 col-md-4 col-sm-6 col-xs-12 info_dl">
            <dt>LAST BLOCK</dt>
            <dd>{{ connected ? lastBlock : `--` }}</dd>
          </dl>
          <dl class="col-lg-4 col-md-4 col-sm-6 col-xs-12 info_dl">
            <dt>TOTAL LIQUID {{ num.viewDenom(bondDenom) }}</dt>
            <dd id="loose_tokens">
              {{
                pool.pool && pool.pool.not_bonded_tokens
                  ? num.shortDecimals(num.atoms(pool.pool.not_bonded_tokens))
                  : `--`
              }}
            </dd>
          </dl>
          <dl class="col-lg-4 col-md-4 col-sm-6 col-xs-12 info_dl">
            <dt>TRANSACTIONS</dt>
            <dd>
              {{
                lastHeader.total_txs !== undefined
                  ? num.shortDecimals(lastHeader.total_txs)
                  : `--`
              }}
            </dd>
          </dl>
          <dl class="col-lg-4 col-md-4 col-sm-6 col-xs-12 info_dl">
            <dt>NUMBER OF VALIDATORS</dt>
            <dd>
              {{
                delegates.delegates.length > 0
                  ? num.shortDecimals(delegates.delegates.length)
                  : `--`
              }}
            </dd>
          </dl>
          
          <dl class="col-lg-4 col-md-4 col-sm-6 col-xs-12 info_dl">
            <dt>TOTAL DELEGATED {{ num.viewDenom(bondDenom) }}</dt>
            <dd id="bonded_tokens">
              {{
                pool.pool && pool.pool.bonded_tokens
                  ? num.shortDecimals(num.atoms(pool.pool.bonded_tokens))
                  : `--`
              }}
            </dd>
          </dl>
        </div>
      </div>
      <table v-if="connected" class="blocks data-table">
        <thead>
          <PanelSort :properties="properties" />
        </thead>
        <tbody>
          <tr v-if="blocks.length === 0" class="block data-table__row">
            <td>
              <img class="loading-icon" src="~assets/images/loader.svg" />
            </td>
            <td>
              <img class="loading-icon" src="~assets/images/loader.svg" />
            </td>
          </tr>
          <tr
            v-for="block in blocks"
            :key="block.header.height"
            class="block data-table__row"
          >
            <td>
              <router-link
                :to="{
                  name: `block`,
                  params: { height: block.header.height }
                }"
              >
                {{ `#` + num.prettyInt(block.header.height) }}
              </router-link>
            </td>
            <td>{{ block.header.num_txs }}</td>
          </tr>
        </tbody>
      </table>
    </template>
  </TmPage>
</template>

<script>
import moment from "moment"
import { mapGetters } from "vuex"
import num from "scripts/num"
import PanelSort from "staking/PanelSort"
import TmPage from "common/TmPage"
export default {
  name: `page-network`,
  components: {
    PanelSort,
    TmPage
  },
  data: () => ({
    num
  }),
  computed: {
    ...mapGetters([
      `connected`,
      `lastHeader`,
      `delegates`,
      `pool`,
      `bondDenom`,
      `blocks`
    ]),
    status() {
      const color = this.connected ? `green` : `red`
      const message = this.connected
        ? `Network is up and running`
        : `Not connected to a node`
      return { color, message }
    },
    lastBlock() {
      moment.relativeTimeThreshold(`ss`, 1)
      return moment.utc(this.lastHeader.time).format(`MMM Do YYYY, HH:mm:ssa z`)
    },
    properties() {
      return [
        {
          title: `BLOCK NUMBER`,
          value: `block_number`,
          tooltip: `Block Number`,
          class: `blockNumber`
        },
        {
          title: `TRANSACTIONS`,
          value: `transactions`,
          tooltip: `Number of transactions per block`,
          class: `transactions`
        }
      ]
    }
  },
  mounted() {
    this.$store.dispatch(`getDelegates`)
    this.$store.dispatch(`getPool`)
  }
}
</script>
<style scoped>
.block td {
  padding: 1rem;
}

.page-profile__status-and-title {
  display: block
}

.row {
  margin: 0 !important;
}

.blocks.data-table th {
  padding: 0.5rem 1rem;
}
</style>
