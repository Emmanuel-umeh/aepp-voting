<template>
  <ae-modal
    v-if="createProposalModalShown"
    title="Create statement"
    @close="toggleCreateProposalModal">
    <div class="create-proposal-modal">
      <label :for="_uid">Statement</label>
      <input
        v-focus.lazy="true"
        v-model="statement"
        :id="_uid"
        placeholder="Enter a short statement text ..."
      >

      <p>Choose one or multiple categories</p>
      <tags-select v-model="tags" />

      <p>Agree or disagree and sign your vote</p>
      <sign-statement
        :statement="statement"
        :signature-handler="signatureHandler"
      />
    </div>
  </ae-modal>
</template>

<script>
import { mapState, mapMutations } from 'vuex';
import { focus } from 'vue-focus';
import { AeModal } from '@aeternity/aepp-components';
import SignStatement from './SignStatement.vue';
import TagsSelect from './TagsSelect.vue';

export default {
  components: {
    AeModal,
    SignStatement,
    TagsSelect,
  },
  directives: { focus },
  data() {
    return {
      statement: '',
      tags: [],
    };
  },
  computed: mapState(['createProposalModalShown']),
  methods: {
    ...mapMutations(['toggleCreateProposalModal']),
    async signatureHandler({ signature, upVote }) {
      await this.$store.dispatch('createProposal', {
        statement: this.statement,
        signature,
        upVote,
        tags: this.tags,
      });
      this.$router.push({
        name: 'proposal-list',
        params: { sort: 'newest' },
      });
    },
  },
};
</script>

<style lang="scss">
@import '/node_modules/@aeternity/aepp-components/dist/variables';

.create-proposal-modal {
  > * {
    margin: 15px 0;
  }

  > label,
  > input {
    display: block;
  }

  label,
  p,
  .sign-message > div:first-child,
  .sign-message > label {
    font-weight: 500;
  }

  p,
  .sign-message > div:first-child,
  .sign-message > label {
    margin-top: 50px;
    text-align: left;
  }

  > input,
  .sign-message > input {
    box-sizing: border-box;
    width: 100%;
    height: 44px;
    padding: 7px;
    font-size: 24px;
    border: solid 1px $grey;
    border-radius: 4px;
    box-shadow: none;
  }
}
</style>
