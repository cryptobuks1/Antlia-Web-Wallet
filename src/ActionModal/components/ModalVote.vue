<template>
  <ActionModal
    id="modal-vote"
    ref="actionModal"
    :validate="validateForm"
    title="Vote"
    class="modal-vote"
    :transaction-data="transactionData"
    :notify-message="notifyMessage"
    @close="clear"
    v-if="session.signedIn"
  >
  <!-- submission-error-prefix="Voting failed" -->
    <TmFormGroup class="action-modal-group vote-options">
      <TmBtn
        id="vote-yes"
        :class="[vote === `Yes` ? 'active' : '']"
        :disabled="lastVoteOption === `Yes`"
        color="secondary"
        value="Yes"
        size="md"
        @click.native="vote = 'Yes'"
      />
      <TmBtn
        id="vote-no"
        :class="[vote === `No` ? 'active' : '']"
        :disabled="lastVoteOption === `No`"
        color="secondary"
        value="No"
        size="md"
        @click.native="vote = 'No'"
      />
      <!-- <TmBtn
        id="vote-veto"
        :class="[vote === `NoWithVeto` ? 'active' : '']"
        :disabled="lastVoteOption === `NoWithVeto`"
        color="secondary"
        value="No With Veto"
        size="md"
        @click.native="vote = 'NoWithVeto'"
      /> -->
      <TmBtn
        id="vote-abstain"
        :class="[vote === `Abstain` ? 'active' : '']"
        :disabled="lastVoteOption === `Abstain`"
        color="secondary"
        value="Abstain"
        size="md"
        @click.native="vote = 'Abstain'"
      />
      
      <TmFormMsg
        v-if="$v.vote.$error && !$v.vote.required"
        name="Vote"
        type="required"
      />
      <TmFormMsg
        v-if="delegated < 50000"
        :msg="`You can't vote, Delegated CLR must be greater than or equal to 50,000 CLR`"
        type="custom"
        class="margintop"
      />
    </TmFormGroup>
  </ActionModal>
</template>

<script>
import { mapGetters } from "vuex"
import num from "../../scripts/num"
import { required } from "vuelidate/lib/validators"
import ActionModal from "./ActionModal"
import TmBtn from "src/components/common/TmBtn"
import TmFormGroup from "src/components/common/TmFormGroup"
import TmFormMsg from "src/components/common/TmFormMsg"

import transaction from "../utils/transactionTypes"

const isValid = option =>
  option === `Yes` ||
  option === `No` ||
  // option === `NoWithVeto` ||
  option === `Abstain`

export default {
  name: `modal-vote`,
  components: {
    ActionModal,
    TmBtn,
    TmFormGroup,
    TmFormMsg
  },
  props: {
    proposalId: {
      type: [Number, String],
      required: true
    },
    proposalTitle: {
      type: String,
      required: true
    },
    lastVoteOption: {
      default: undefined,
      type: String
    }
  },
  data: () => ({
    vote: null,
    num
  }),
  computed: {
    ...mapGetters([`bondDenom`,`oldBondedAtoms`,`session`]),
    transactionData() {
      return {
        type: transaction.VOTE,
        proposalId: this.proposalId,
        option: this.vote
      }
    },
    notifyMessage() {
      return {
        title: `Successful vote!`,
        body: `You have successfully voted ${this.vote} on proposal #${
          this.proposalId
        }`
      }
    },
    delegated() {
      return this.num.atoms(this.oldBondedAtoms)
    },
  },
  validations() {
    return {
      vote: {
        required,
        isValid
      }
    }
  },
  methods: {
    open() {
      this.$refs.actionModal.open()
    },
    validateForm() {
      if (this.delegated < 50000) {
        return 0
      }
      this.$v.$touch()

      return !this.$v.$invalid
    },
    clear() {
      this.$v.$reset()

      this.vote = null
    }
  },
  mounted() {
    this.oldBondedAtoms
  }
}
</script>

<style>
.proposal-title {
  font-weight: 500;
}

.action-modal-group.tm-form-group.vote-options {
  padding: 2rem 0;
}

.radio-container {
  display: flex;
  align-items: center;
  padding: 0.25rem 0;
}

.margintop {
  margin-top: 1rem !important;
}

.radio-container label {
  padding-left: 0.5rem;
}

.vote-options button {
  margin: 0;
  min-width: 49%;
}

.vote-options button span {
  margin: 0.25rem;
}

.vote-options button.active span {
  background: #49395c;
  border-radius: 5px;
  padding: 1px 43px;
}
</style>
