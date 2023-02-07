<template>
	<div class="content-body-outer my-market-event-detail-page" v-if="matka != null">
    <div class="transaction-history-nav-outer">
      <ul class="transaction-history-nav">
        <li>
          <a href="javascript:void(0)">
            <span class="text-danger-2">{{matka.title.toUpperCase()}}</span> &nbsp; <span class="text-info">{{parse_worli_date}}</span> &nbsp; PROFIT &amp; LOSS DETAILS
          </a>
        </li>
      </ul>
    </div>
    <div v-if="is_loading" style="position: relative; min-height: 400px">
      <loader></loader>
    </div>
    <div v-else class="d-flex flex-row event-detail-page-block">
      <div class="content-body-main-block">
        <div class="chart-main-block-outer-single">
          <div class="chart-main-block">
            <div class="chart-event-header-outer">
              <div class="chart-event-title-block-outer d-flex flex-row" v-if="matka != null">
                <div class="chart-event-title-block">
                  <h3 class="chart-event-title">{{matka.title.toUpperCase()}} <span class="text-info">{{parse_worli_date}}</span></h3>
                </div>
              </div>
              <div class="chart-event-header-menu-link" v-if="mainMarketsData != null">
                <div class="chart-event-tab-content tab-content">
                  <div class="profit-loss-main-data-block table-responsive" v-if="current_user != null">
                    <table class="table" v-if="current_user.role.role == 'is_user'">
                      <thead class="thead">
                        <tr>
                          <th>MARKET TYPE</th>
                          <th>P &amp; L</th>
                          <th>Result</th>
                          <th>BETS</th>
                        </tr>
                      </thead>
                      <tbody>
                        <tr>
                          <td>OPEN</td>
                          <td :class="{ 'text-danger-2' : (mainMarketsData.open_pl < 0), 'text-success' : (mainMarketsData.open_pl > 0)}"><b>{{mainMarketsData.open_pl | positive | addCommas}}</b></td>
                          <td>{{mainMarketsData.open_result != null ? mainMarketsData.open_result + ' - ': ''}} {{ mainMarketsData.open_main_result }}</td>
                          <td width="5%"><a href="javascript:void(0)"><i class="material-icons" style="font-size: 18px;" @click="getMarketBets('open')">open_in_new</i></a></td>
                        </tr>
                        <tr>
                          <td>CLOSE</td>
                          <td :class="{ 'text-danger-2' : (mainMarketsData.close_pl < 0), 'text-success' : (mainMarketsData.close_pl > 0)}"><b>{{mainMarketsData.close_pl | positive | addCommas}}</b></td>
                          <td>{{mainMarketsData.close_result != null ? mainMarketsData.close_result + ' - ': ''}} {{ mainMarketsData.close_main_result }}</td>
                          <td width="5%"><a href="javascript:void(0)"><i class="material-icons" style="font-size: 18px;" @click="getMarketBets('close')">open_in_new</i></a></td>
                        </tr>
                        <tr>
                          <td>JODI</td>
                          <td :class="{ 'text-danger-2' : (mainMarketsData.jodi_pl < 0), 'text-success' : (mainMarketsData.jodi_pl > 0)}"><b>{{mainMarketsData.jodi_pl | positive | addCommas}}</b></td>
                          <td>{{mainMarketsData.jodi_result}}</td>
                          <td width="5%"><a href="javascript:void(0)"><i class="material-icons" style="font-size: 18px;" @click="getMarketBets('jodi')">open_in_new</i></a></td>
                        </tr>
                      </tbody>
                      <tfoot class="thead">
                        <th>TOTAL</th>
                        <th :class="{ 'text-danger-2' : (allTotalTotal < 0), 'text-success' : (allTotalTotal > 0)}"><b>{{allTotalTotal | positive | addCommas}}</b></th>
                        <th></th>
                        <th></th>
                      </tfoot>
                    </table>
                    <table class="table" v-else>
                      <thead class="thead">
                        <tr>
                          <th>MARKET TYPE</th>
                          <th>P &amp; L</th>
                          <th>RESULT</th>
                          <th>BET COUNT</th>
                          <th>BETS</th>
                        </tr>
                      </thead>
                      <tbody>
                        <tr>
                          <td>OPEN</td>
                          <td :class="{ 'text-danger-2' : (mainMarketsData.open_pl > 0), 'text-success' : (mainMarketsData.open_pl < 0)}"><b>{{mainMarketsData.open_pl | positive | addCommas}}</b></td>
                          <td>{{mainMarketsData.open_result}} - {{ mainMarketsData.open_main_result }}</td>
                          <td width="5%">{{ mainMarketsData.open_bet_count }}</td>
                          <td width="5%"><a href="javascript:void(0)"><i class="material-icons" style="font-size: 18px;" @click="getMarketBets('open')">open_in_new</i></a></td>
                        </tr>
                        <tr>
                          <td>CLOSE</td>
                          <td :class="{ 'text-danger-2' : (mainMarketsData.close_pl > 0), 'text-success' : (mainMarketsData.close_pl < 0)}"><b>{{mainMarketsData.close_pl | positive | addCommas}}</b></td>
                          <td>{{mainMarketsData.close_result}} - {{ mainMarketsData.close_main_result }}</td>
                          <td width="5%">{{ mainMarketsData.close_bet_count }}</td>
                          <td width="5%"><a href="javascript:void(0)"><i class="material-icons" style="font-size: 18px;" @click="getMarketBets('close')">open_in_new</i></a></td>
                        </tr>
                        <tr>
                          <td>JODI</td>
                          <td :class="{ 'text-danger-2' : (mainMarketsData.jodi_pl > 0), 'text-success' : (mainMarketsData.jodi_pl < 0)}"><b>{{mainMarketsData.jodi_pl | positive | addCommas}}</b></td>
                          <td>{{mainMarketsData.jodi_result}}</td>
                          <td width="5%">{{ mainMarketsData.jodi_bet_count }}</td>
                          <td width="5%"><a href="javascript:void(0)"><i class="material-icons" style="font-size: 18px;" @click="getMarketBets('jodi')">open_in_new</i></a></td>
                        </tr>
                      </tbody>
                      <tfoot class="thead">
                        <th>TOTAL</th>
                        <th :class="{ 'text-danger-2' : (allTotalTotal > 0), 'text-success' : (allTotalTotal < 0)}"><b>{{allTotalTotal | positive | addCommas}}</b></th>
                        <th></th>
                        <th>{{ mainMarketsData | countTotalBets }}</th>
                        <th></th>
                      </tfoot>
                    </table>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="content-body-sidebar content-body-sidebar-no-scroll">
        <div class="content-body-sidebar-block clients-main-block" v-if="current_user != null && current_user.role.role != 'is_user'">
          <div class="section-heading-block">
            <div class="row">
              <div class="col-8">
                <h5 class="section-heading">CLIENTS</h5>
              </div>
              <div class="col-4 text-right">
                <a href="javascript:void(0)" v-if="current_user != null && current_user.id != auth.id" @click.prevent="getPreviousData" class="text-info" style="position: relative; top: 6px;">BACK</a>
              </div>
            </div>
          </div>
          <div class="sidebar-info-block-outer pad-0" style="max-height: 200px; overflow-x: auto; overflow-y: auto;">
            <table class="table">
              <thead class="thead">
                <tr>
                  <th class="text-left">UID</th>
                  <th class="text-left">P &amp; L</th>
                </tr>
              </thead>
              <tbody v-if="clients.length > 0">
                <tr v-for="client in clients">
                  <th>
                    <a @click.prevent="showClientsData(client.id)" v-if="client.role != 'is_user'" class="text-primary" href="javascript:void(0)">{{client.userid.toUpperCase()}}</a>
                    <div v-if="client.role == 'is_user'">{{client.userid.toUpperCase()}}</div>
                  </th>
                  <th :class="client.p_l > 0 ? 'text-danger-2' : 'text-success'">{{client.p_l | positive | addCommas}}</th>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>
    <!-- Bets Modal -->
    <modal name="bets-modal"
          transition="nice-modal-fade"
          :scrollable="true"
          :draggable="false"
          :adaptive="true"
          height="auto"
          :min-width="400"
          :max-width="900"
          width="100%"
          :max-height="1000">
      <div>
        <div class="modal-header">
          <h4 class="modal-title">{{current_market_title}} <span class="badge badge-danger">{{currentBets.length}}</span></h4>
          <a href="javascript:void(0)" class="default-close-btn" title="Close" @click="$modal.hide('bets-modal')"><i class="material-icons">close</i></a>
        </div>
        <div class="modal-body">
          <div class="card-body" style="height: 100%; padding-left: 0; padding-right: 0;">
            <div v-if="currentBets.length > 0">
              <div class="vue-modal-scroll">
                <div class="table-responsive">
                  <table class="table">
                    <thead class="thead">
                      <tr>
                        <th class="text-left" v-if="auth._role != 'is_user' && auth._role != 'is_master'">Parent</th>
                        <th class="text-left" v-if="auth._role != 'is_user'">User</th>
                        <th class="text-left">Market Type</th>
                        <th class="text-left">Selection</th>
                        <th class="text-left">Odds</th>
                        <th class="text-left">Rate</th>
                        <th class="text-left">Stake</th>
                        <th class="text-left">Date</th>
                        <th class="text-left">P &amp; L</th>
                        <!-- <th class="text-left">Commission</th>
                        <th class="text-left">Total P &amp; L</th> -->
                      </tr>
                    </thead>
                    <tbody>
                      <tr v-for="(bet, index) in currentBets">
                        <th class="text-left" v-if="auth._role != 'is_user' && auth._role != 'is_master'">
                            <a href="javascript:void(0)" style="margin-right: 5px;" @click="showDeleteBet(bet.id)" v-if="auth._role == 'is_director'">
                                <i class="material-icons" style="color: red; font-size: 16px; position: relative;">delete</i>
                            </a>
                            {{bet.client_parent_userid}}
                        </th>
                        <th class="text-left" v-if="auth != null && auth._role != 'is_user'">{{bet.userid}}</th>
                        <th class="text-left">{{bet.worli_matka_type.replace('_', ' ').toUpperCase()}}</th>
                        <th class="text-left">{{bet.selection}}</th>
                        <th class="text-left text-primary">{{bet.odds}}</th>
                        <th class="text-left text-primary">{{bet.rate}}</th>
                        <th class="text-left">{{bet.stake | positive | addCommas}}</th>
                        <th class="text-left">{{bet.created_at}}</th>
                        <th class="text-left" :class="{ 'text-danger-2' : (bet.loss != 0), 'text-success' : (bet.profit != 0)}">{{(bet.profit != 0 ? (bet.profit) : (bet.loss)) | positive | addCommas}}</th>
                        <!-- <th class="text-left text-primary">{{(bet.commission != null ? bet.commission : 0) | positive | addCommas}}</th>
                        <th class="text-left" :class="{ 'text-danger-2' : (bet.loss != 0), 'text-success' : (bet.profit != 0)}">{{(bet.profit != 0 ? bet.profit : bet.loss) | positive | addCommas}}</th> -->
                      </tr>
                    </tbody>
                  </table>
                </div>
              </div>
            </div>
            <div v-else>
              <div style="padding-bottom: 30px;">
                <h4 class="text-center" style="margin: 20px 0 5px;">No bet placed yet !</h4>
              </div>
            </div>
          </div>
        </div>
      </div>
    </modal>
    <!-- Delete Modal -->
    <modal name="delete-modal"
          transition="nice-modal-fade"
          :scrollable="true"
          :draggable="true"
          :adaptive="true"
          max-width="100%"
          height="auto"
          :min-width="100"
          :max-width="300"
          :max-height="1000">
      <div>
        <div class="modal-header">
          <h4 class="modal-title">DELETE BET ?</h4>
          <a href="javascript:void(0)" class="default-close-btn" title="Close" @click="$modal.hide('delete-modal')"><i class="material-icons">close</i></a>
        </div>
        <div class="modal-body" style="padding: 10px;">
          <div class="card-body">
            <div class="d-flex flex-row justify-content-between">
              <a href="javascript:void(0)" class="btn btn-info" @click="deleteBet('VOID')">VOID</a>
              <a href="javascript:void(0)" class="btn btn-warning" @click="deleteBet('INVALID')">INVALID</a>
              <a href="javascript:void(0)" class="btn btn-danger" @click="deleteBet('DELETE')">DELETE</a>
            </div>
          </div>
        </div>
      </div>
    </modal>
    <!-- Delete Reason Modal -->
    <modal name="delete-bet-reason-modal" transition="nice-modal-fade" :clickToClose="false" :scrollable="true" :min-width="100" :max-width="300" height="auto" :draggable="false" :adaptive="true" style="z-index:3000">
        <div>
            <div class="modal-header">
                <h4 class="modal-title">DELETE BET REASON ?</h4>
                <a href="javascript:void(0)" class="default-close-btn" title="Close" @click="$modal.hide('delete-bet-reason-modal')"><i class="material-icons">close</i></a>
            </div>
            <div class="modal-body" style="padding: 10px">
                <div class="card-body">
                    <!--<div class="d-flex flex-row justify-content-between">-->
                        <a href="javascript:void(0)" class="btn btn-warning btm-padding" @click="deleteBetWithReason('Late no ball')">Late no ball</a>
                        <a href="javascript:void(0)" class="btn btn-warning btm-padding" @click="deleteBetWithReason('Reduced over')">Reduced over</a>
                        <a href="javascript:void(0)" class="btn btn-warning btm-padding" @click="deleteBetWithReason('Wrong odd')">Wrong odd</a>
                        <a href="javascript:void(0)" class="btn btn-warning btm-padding" @click="deleteBetWithReason('Technical issue')">Technical issue</a>
                        <a href="javascript:void(0)" class="btn btn-warning btm-padding" @click="deleteBetWithReason('Agent mistake')">Agent mistake</a>
                        <a href="javascript:void(0)" class="btn btn-warning btm-padding" @click="deleteBetWithReason('Cheating')">Cheating</a>
                        <a href="javascript:void(0)" class="btn btn-warning btm-padding" @click="deleteBetWithReason('Late Suspended')">Late Suspended</a>
                        <a href="javascript:void(0)" class="btn btn-warning btm-padding" @click="deleteBetWithReason('Wrong result')">Wrong result</a>
                        <a href="javascript:void(0)" class="btn btn-warning btm-padding" @click="deleteBetWithReason('2nd inning bet')">2nd inning bet</a>
                        <a href="javascript:void(0)" class="btn btn-warning btm-padding" @click="deleteBetWithReason('Player not opened')">Player not opened</a>
                        <a href="javascript:void(0)" class="btn btn-warning btm-padding" @click="deleteBetWithReason('wicket not fall')">wicket not fall</a>
                        <a href="javascript:void(0)" class="btn btn-warning btm-padding" @click="deleteBetWithReason('Over not completed')">Over not completed</a>
                        <a href="javascript:void(0)" class="btn btn-warning btm-padding" @click="deleteBetWithReason(' Wrong Fancy')"> Wrong Fancy</a>
                        <a href="javascript:void(0)" class="btn btn-warning btm-padding" @click="deleteBetWithReason('Same IP')">Same IP</a>
                        <a href="javascript:void(0)" class="btn btn-warning btm-padding" @click="deleteBetWithReason('Ground Bet')">Ground Bet</a>
                        <a href="javascript:void(0)" class="btn btn-warning btm-padding" @click="deleteBetWithReason('Multiple Account Bet')">Multiple Account Bet</a>
                    <!--</div>-->
                </div>
            </div>
        </div>
    </modal>
  </div>
</template>
<script>
  import { mapState } from 'vuex';
  export default {
    props: ['matka_slug', 'worli_date'],
    data() {
      return {
        current_market_title: '',
        parse_worli_date: '',
        current_user: null,
        is_current_position: true,
        is_loading: false,
        matka: null,
        clients: [],
        mainMarketsData: null,
        bets: [],
        currentBets: [],
        init: true,
        is_current_position: true,
        bet_delete_status:'',
      }
    },
    computed: {
      allTotalTotal() {
        var pl = this.mainMarketsData.open_pl + this.mainMarketsData.close_pl + this.mainMarketsData.jodi_pl;
        return pl;
      },
      ...mapState([
        'loggedIn', 'userData', 'auth', 'origin_path', 'mobileDevice'
      ])
    },
    filters: {
      countTotalBets: function(val) {
          return parseInt(val.close_bet_count) + parseInt(val.jodi_bet_count) + parseInt(val.open_bet_count)
      },
      positive: function (val) {
        if (val < 0) {
          return val * -1;
        } else {
          return val;
        }
      },
      addCommas: function (val) {
        var total = val;
        if (val != '' && val != null) {
          var val = parseFloat(val).toFixed(2);
          val += '';
          var x = val.split('.');
          var x1 = x[0];
          var x2 = x.length > 1 ? '.' + x[1] : '';
          var rgx = /(\d+)(\d{3})/;
          while (rgx.test(x1)) {
              x1 = x1.replace(rgx, '$1' + ',' + '$2');
          }
          total = x1 + x2;
        }
        return total;
      }
    },
    watch: {
      auth: function (val) {
        if (val._role != '' && this.init) {
          this.current_user = val;
          this.init = false;
        }
      }
    },
    async created() {
      this.is_loading = true;
      $('body').removeClass('event-page');
      await this.getMatka();
    },
    methods: {
        showDeleteBet(id) {
            this.selected_bet_id = id;
            this.$modal.show('delete-modal');
        },
        deleteBet(type) {
            this.bet_delete_status  =   type;
            this.$modal.show("delete-bet-reason-modal");
        },
        deleteBetWithReason(reason) {
            this.$modal.hide("delete-bet-reason-modal");
            this.$modal.hide("delete-modal");
            if (this.selected_bet_id != null) {
                axios.post("/delete-bet", { id: this.selected_bet_id, type: this.bet_delete_status.toString(), 'reason':reason}).then((res) => {
                    if (res.data.error != null) {
                        this.$toasted.error(res.data.error, { icon: "not_interested" });
                    } else {
                        this.selected_bet_id = null;
                        this.$toasted.show('BET '+ this.bet_delete_status.toString() + 'ED' +' SUCCESSFULLY', {icon: 'check_circle'});
                    }
                }).catch((e) => {
                    this.$toasted.error('Please try again..!', { icon: "not_interested" });
                });
            }
        },
      isset(val) {
        if (typeof val === 'undefined') {
          return false;
        } else {
          return true;
        }
      },
      async getMatka() {
        await axios.get('/get-matka/'+this.$props.matka_slug).then(res => {
          this.matka = res.data.matka;
          this.current_user = this.auth;
          this.getData();
        }).catch(e => {
          this.getMatka();
          console.log(e.response);
        });
      },
      async getData() {
        this.is_loading = true;
        await axios.get('/get-worli-matka-pl-data-by-slug-and-date/'+this.$props.matka_slug+'/'+this.$props.worli_date).then(res => {
            this.parse_worli_date = res.data.parse_worli_date;
            this.current_user = this.auth;
            this.bets = res.data.all_bets;
            this.mainMarketsData = res.data.mainMarketsData;
            if(this.$props.matka_slug == 'faridabad' || this.$props.matka_slug == 'gali' || this.$props.matka_slug == 'gaziabad' || this.$props.matka_slug == 'desawar') {
                this.mainMarketsData.open_result = null;
                this.mainMarketsData.close_result = null;
            }
            this.clients = res.data.clients;
        }).catch(e => {
          this.getData();
          console.log(e.response);
        });
        this.is_loading = false;
      },
      async showClientsData(id) {
        this.is_current_position = false;
        this.is_loading = true;
        await axios.get('/get-worli-matka-client-pl-data-by-slug-and-date/'+this.$props.matka_slug+'/'+this.$props.worli_date+'/'+id).then(res => {
            this.current_user = res.data.current_user;
            this.bets = res.data.all_bets;
            this.mainMarketsData = res.data.mainMarketsData;
            if(this.$props.matka_slug == 'faridabad' || this.$props.matka_slug == 'gali' || this.$props.matka_slug == 'gaziabad' || this.$props.matka_slug == 'desawar') {
                this.mainMarketsData.open_result = null;
                this.mainMarketsData.close_result = null;
            }
          this.clients = res.data.clients;
        }).catch(e => {
          this.showClientsData(id);
          console.log(e.response);
        });
        this.is_loading = false;
      },
      async getPreviousData() {
        var id = this.auth.id;
        if (this.auth.role.role == 'is_manager') {
          id = 1;
        }
        if (this.current_user != null && this.current_user != '' && this.current_user.parent_id === id) {
          this.getData();
        } else if (this.current_user != null && this.current_user != '') {
          this.showClientsData(this.current_user.parent_id);
        }
      },
      getMarketBets(type) {
        axios.get('/get-worli-matka-bets-by-slug-and-date-type/'+this.$props.matka_slug+'/'+this.$props.worli_date+'/'+type).then(res => {
            this.current_market_title = type.toUpperCase();
            this.currentBets = [];
            this.currentBets = res.data.bets;
            this.$modal.show('bets-modal');
        }).catch(e => {
          this.showClientsData(id);
          console.log(e.response);
        });
      }
    }
  };
</script>
<style scoped>
  .table th,
  .table td {
    white-space: nowrap;
    border: 1px solid #CCC;
  }
  .clients-main-block .table th,
  .clients-main-block .table td {
    padding: 5px 12px;
  }
  .thead {
    background-color: #EEE;
  }
  .section-heading-block {
    padding: 0 10px !important;
  }
  @media (min-width: 1280px) {
    .content-body-main-block {
      max-width: calc(100% - 400px);
    }
    .content-body-sidebar {
      width: 400px;
    }
  }
  @media (max-width: 1279px) {
    .content-body-sidebar {
      display: block !important;
    }
    .content-body-sidebar {
      width: 100%;
    }
  }
  .v--modal-box .modal-body,
  .v--modal-box .card-body {
    padding: 0;
    overflow: hidden;
  }
  .vue-modal-sub-header {
    box-shadow: 0 5px 20px rgba(0,0,0,0.1);
    padding: 10px;
  }
  .vue-modal-scroll {
    height: 100%;
    max-height: 500px;
    overflow-x: hidden;
    overflow-y: auto;
  }
  .vue-modal-scroll h5 {
    font-size: 18px;
    padding: 10px;
    border-bottom: 1px solid #EEE;
  }
  .vue-modal-scroll .book-arrow-block {
    position: absolute;
    top: 50%;
    right: -10px;
    z-index: 1;
    transform: translateY(-50%);
  }
  .transaction-history-nav {
    margin-bottom: 0;
  }
  .btm-padding { margin-bottom: 5px; }
</style>
