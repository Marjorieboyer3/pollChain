<template>
  <div>
    <div>

      <div class="bloc-title">
        <div class="small-title">TOP 3 : SIGN PETITION  THIS MONTH</div>
        <div class="big-title">Your opinion start<br>
          to matter here & now.
        </div>
      </div>

      <div
        v-for="poll in pollsFree"
        v-bind:key="JSON.stringify(poll)"
        class="card w-75"
      >
        <div class="card-body">
          <h5 class="card-title">{{poll.title}}</h5>
          <div>Contributors: {{poll.contributors}} - target: {{poll.target}}</div>
          <p class="card-text">{{poll.query}}</p>
          <button class="btn btn-primary" type="button" @click="onVote(poll.title)">I sign</button>
        </div>
      </div>

      <div class="bloc-title">
        <div class="small-title">SUCCESS STORIES : LAST MONTH</div>
        <div class="big-title">See what change we made<br>
          with optimism & determination
        </div>
      </div>

      <div class="card-group">
        <div class="card">

          <div class="card-body" id="designcard">
            <h5 class="card-title">Rohingya massacre : <br>UN must intervene</h5>
            <p class="card-text">This is a wider card with supporting text below as a natural lead-in to additional content. This content is a little bit longer.</p>
          </div>
          <div class="card-footer">
            <div class="signed-num">300 000 signed. <br>
              Go to 300 000 petitors</div>
            <div class="progress">
              <div class="progress-bar bg-warning" role="progressbar" style="width: 100%" aria-valuenow="75" aria-valuemin="0" aria-valuemax="100"></div>
            </div>
          </div>
        </div>
        <div class="card">

          <div class="card-body">
            <h5 class="card-title">Opening our hearts to refugees</h5>
            <p class="card-text">This card has supporting text below as a natural lead-in to additional content.</p>
          </div>
          <div class="card-footer">
            <div class="signed-num">300 000 signed. <br>
              Go to 300 000 petitors</div>
            <div class="progress">
              <div class="progress-bar bg-warning" role="progressbar" style="width: 100%" aria-valuenow="75" aria-valuemin="0" aria-valuemax="100"></div>
            </div>
          </div>
        </div>
        <div class="card">

          <div class="card-body">
            <h5 class="card-title">Protecting forests, the lungs of our planet</h5>
            <p class="card-text">This is a wider card with supporting text below as a natural lead-in to additional content. This card has even longer content than the first to show that equal height action.</p>
          </div>
          <div class="card-footer">
            <div class="signed-num">300 000 signed. <br>
              Go to 300 000 petitors</div>
            <div class="progress">
              <div class="progress-bar bg-warning" role="progressbar" style="width: 100%" aria-valuenow="75" aria-valuemin="0" aria-valuemax="100"></div>
            </div>
          </div>
        </div>
      </div>

      <div class="bloc-title">
        <div id="small-title">VOTING FOR THE NEXT PETITION</div>
        <div id="big-title">Upgrade the  3 next<br>
          petition for next month
        </div>
      </div>

      <div class="card w-75">
        <div class="card-body">

          <h5 class="card-title">#1 Free us from the plastic bag</h5>
          <p class="card-text">Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. </p>
          <a href="#" class="btn btn-primary">up</a>
        </div>
      </div>

      <div class="card w-75">
        <div class="card-body">

          <h5 class="card-title">#2 Save the Amazonia recif</h5>
          <p class="card-text">Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. </p>
          <a href="#" class="btn btn-primary">up</a>
        </div>
      </div>

      <div class="card w-75">
        <div class="card-body">

          <h5 class="card-title">#3 Stop riding Thaï Elephant</h5>
          <p class="card-text">Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. </p>
          <a href="#" class="btn btn-primary">up</a>
        </div>
      </div>
    </div>
    <div v-if="errors.length === 0 && isSending" id="send-waiting">
      Transaction is sending, Waiting....
    </div>
    <div v-if="errors.length > 0" id="errors">
      <div>Error{{ errors.length > 1 ? "s" : "" }}: </div>
      <div
        v-for="error in errors"
        v-bind:key="error"
      >{{ error }}</div>
    </div>
    <b-modal
      :visible="showAskSponsoring"
      :centered="true"
      :hideHeaderClose="true"
      :noCloseOnBackdrop="true"
      :noCloseOnEsc="true"
      :hideFooter="true"
    >
      <div>do you want sponsored ?</div>
      <button class="btn btn-primary card-popup-btn" type="button" @click="onSponsored()">Yes</button>
      <button class="btn btn-primary card-popup-btn" type="button" @click="validateVote()">No</button>
    </b-modal>
    <b-modal
      :visible="showSponsoring"
      :centered="true"
      :hideHeaderClose="true"
      :noCloseOnBackdrop="true"
      :noCloseOnEsc="true"
      :hideFooter="true"
    >
      <div class="card-popup-sponsored">(Content sponsored for free usage)</div>
      <div v-if="pollsSponsoring.length > 0" class="card-popup card w-75">
        <div class="card-body">
          <h5 class="card-title">{{pollsSponsoring[0].title}}</h5>
          <p class="card-text-popup">{{pollsSponsoring[0].query}}</p>
        </div>
      </div>
      <button class="btn card-popup-btn btn-primary" type="button" @click="onVoteSponsored(pollsSponsoring[0].title, false)">Yes</button>
      <button class="btn card-popup-btn btn-primary" type="button" @click="onVoteSponsored(pollsSponsoring[0].title, true)">No</button>
    </b-modal>
  </div>
</template>

<script>
  import { mapGetters, mapActions } from 'vuex';
  import bModal from 'bootstrap-vue/es/components/modal/modal'
  import Errors from '@/utils/errors';
  import '@/css/poll.css';

  export default {
    name: 'Poll',
    data: function () {
      return {
        title: '',
        titleSponsored: '',
        voteRejected: false,
        target: 100,
        isSponsoring: false,
        errors: [],
        isSending: false,
        showAskSponsoring: false,
        showSponsoring: false
      };
    },
    computed: {
      ...mapGetters(['pollsFree', 'pollsSponsoring'])
    },
    methods: {
      ...mapActions([ 'vote']),
      onVote(title){
        this.title = title;

        if(this.pollsSponsoring.length > 0) {
          this.showAskSponsoring = true;
        } else {
          this.validateVote();
        }
      },
      onVoteSponsored(titleSponsored, voteRejected){
        this.titleSponsored = titleSponsored;
        this.voteRejected = voteRejected;
        this.validateVote();
      },
      onSponsored(){
        this.showAskSponsoring = false;
        this.showSponsoring = true;
      },
      validateVote(){
        this.errors = [];
        this.isSending = true;
        this.showAskSponsoring = false;
        this.showSponsoring = false;
        this.vote({
          title: this.title,
          titleSponsored: this.titleSponsored,
          voteRejected: this.voteRejected
        }).then(() => {
          this.isSending = false;
        })
          .catch(error => {
            this.errors.push(Errors.displayError(error));
            this.isSending = false;
          });
      }
    },
    components: {
      'b-modal': bModal
    }
  };
</script>

<style scoped lang="scss">
</style>
