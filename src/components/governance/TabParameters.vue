<template>
  <div>
    <div class="parameters__details parameters__section">
      <div class="row">
          <dl class="info_dl col-lg-4 col-md-4 col-sm-12 col-xs-12">
            <dt>
              Minimum Deposit
              <i
                id="tooltip-target-1"
                class="material-icons info-button"
              >
                info_outline
              </i>
              <b-tooltip target="tooltip-target-1" triggers="hover">
                {{depositTooltips.min_deposit}}
              </b-tooltip>
            </dt>
            <dd>
              {{
                governanceParameters.parameters.deposit.min_deposit
                  ? minimumDeposit
                  : `--`
              }}
            </dd>
          </dl>
          <dl class="info_dl col-lg-4 col-md-4 col-sm-6 col-xs-12">
            <dt>
              Threshold
              <i
                id="tooltip-target-2"
                class="material-icons info-button"
              >
                info_outline
              </i>
              <b-tooltip target="tooltip-target-2" triggers="hover">
                {{tallyingTooltips.threshold}}
              </b-tooltip>
            </dt>
            <dd>
              {{
                governanceParameters.parameters.tallying.threshold
                  ? `${parseFloat(
                      governanceParameters.parameters.tallying.threshold
                    ) * 100} %`
                  : `--`
              }}
            </dd>
          </dl>
          <dl class="info_dl col-lg-4 col-md-4 col-sm-6 col-xs-12">
            <dt>
              Quorum
              <i
                id="tooltip-target-3"
                class="material-icons info-button"
              >
                info_outline
              </i>
              <b-tooltip target="tooltip-target-3" triggers="hover">
                {{tallyingTooltips.quorum}}
              </b-tooltip>
            </dt>
            <dd>
              {{
                governanceParameters.parameters.tallying.quorum
                  ? `${parseFloat(
                      governanceParameters.parameters.tallying.quorum
                    ) * 100} %`
                  : `--`
              }}
            </dd>
          </dl>
          <!-- <dl class="info_dl col-lg-4 col-md-4 col-sm-6 col-xs-12">
            <dt>
              Maximum Deposit Period
              <i
                v-tooltip.top="depositTooltips.max_deposit_period"
                class="material-icons info-button"
              >
                info_outline
              </i>
            </dt>
            <dd>
              {{
                governanceParameters.parameters.deposit.max_deposit_period
                  ? depositPeriodInDays + ` days`
                  : `--`
              }}
            </dd>
          </dl> -->
        </div>
        <!-- <div class="row">
          
        </div> -->
        <!-- <div class="row"> -->
          <!-- <dl class="info_dl col-lg-4 col-md-4 col-sm-6 col-xs-12">
            <dt>
              Veto
              <i
                v-tooltip.top="tallyingTooltips.veto"
                class="material-icons info-button"
              >
                info_outline
              </i>
            </dt>
            <dd>
              {{
                governanceParameters.parameters.tallying.veto
                  ? `${parseFloat(
                      governanceParameters.parameters.tallying.veto
                    ) * 100} %`
                  : `--`
              }}
            </dd>
          </dl> -->
          <!-- <dl class="info_dl col-lg-4 col-md-4 col-sm-6 col-xs-12">
            <dt>
              Voting Period
              <i
                v-tooltip.top="votingTooltips.voting_period"
                class="material-icons info-button"
              >
                info_outline
              </i>
            </dt>
            <dd>
              {{
                governanceParameters.parameters.voting.voting_period
                  ? votingPeriodInDays + ` days`
                  : `--`
              }}
            </dd>
          </dl> -->
        <!-- </div> -->
      </div>
    </div>
</template>

<script>
import { mapGetters } from "vuex"
import num, { atoms } from "../../scripts/num.js"
export default {
  name: `tab-staking-parameters`,
  data: () => ({
    atoms,
    depositTooltips: {
      description: `Governance deposit parameters for the Color Platform`,
      min_deposit: `Minimum deposit required for a proposal to enter in voting period`,
      max_deposit_period: `Deposits must reach "minimum deposit" threshold in this period of time`
    },
    tallyingTooltips: {
      description: `Tally parameters for governance in the Color Platform`,
      threshold: `Percentage of "Yes" votes required for proposal to pass`,
      veto: `Percentage of "No With Veto" votes required for proposal to be vetoed`,
      quorum: `Percentage of tokens required to vote on a proposal to be considered valid`
    },
    votingTooltips: {
      description: `Voting parameters for governance in Color Platform`,
      voting_period: `Length of voting period for proposals on Color Platform`
    }
  }),
  computed: {
    ...mapGetters([`session`, `governanceParameters`]),
    minimumDeposit() {
      const coin = num.createDisplayCoin(
        this.governanceParameters.parameters.deposit.min_deposit[0]
      )
      return `${coin.amount} ${coin.denom}`
    },
    depositPeriodInDays() {
      return (
        parseInt(
          this.governanceParameters.parameters.deposit.max_deposit_period
        ) /
        (10 ** 9 * 60 * 60 * 24)
      )
    },
    votingPeriodInDays() {
      return (
        parseInt(this.governanceParameters.parameters.voting.voting_period) /
        (10 ** 9 * 60 * 60 * 24)
      )
    }
  },
  async mounted() {
    this.$store.dispatch(`getGovParameters`)
  }
}
</script>
<style>
.parameters__details > .row > .column {
  flex: 1;
}

.parameters__section {
  margin-bottom: 1.5rem;
  width: 100%;
}

.parameters__section h3 {
  padding: 0.5rem 1rem;
  font-size: var(--h3);
  font-weight: 500;
}
</style>
