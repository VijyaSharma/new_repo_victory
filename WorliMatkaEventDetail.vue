<template>
	<div class="content-body-outer my-market-event-detail-page" v-if="matka != null">
    <div class="transaction-history-nav-outer">
      <ul class="transaction-history-nav">
        <li>
          <a href="javascript:void(0)">
            <span class="text-danger-2">{{matka.title.toUpperCase()}}</span> &nbsp; <span class="text-info"></span> FULL MARKET DETAILS
          </a>
        </li>
      </ul>
      <div class="casino-nav casino-nav-msg-block" v-if="matka.msg != null && matka.msg != ''">
        <a href="javascript:void(0)" class="casino-nav-item">
          <marquee>{{ matka.msg }}</marquee>
        </a>
      </div>
    </div>
    <div v-if="is_loading" style="position: relative; min-height: 400px">
      <loader></loader>
    </div>
    <div v-else class="d-lg-flex flex-row event-detail-page-block">
      <div class="content-body-main-block">
        <div class="chart-main-block-outer-single">
          <div class="chart-main-block">
            <div class="chart-event-header-outer">
              <div class="chart-event-title-block-outer d-flex flex-column" v-if="matka != null">
                <div class="chart-event-title-block">
                  <h3 class="chart-event-title">{{matka.title.toUpperCase()}}

                  </h3>
                </div>
                <fieldset style="margin-top: 10px;">
                  <multiselect v-model="selected_type" :allow-empty="false" label="name" track-by="name" :options="all_types" :searchable="true" :close-on-select="true" :show-labels="false" :multiple="false"></multiselect>
                </fieldset>
                <fieldset style="margin-top: 10px;">
                  <multiselect v-model="selected_matka_type" :allow-empty="false" label="name" track-by="name" :options="all_matka_types" :searchable="true" :close-on-select="true" :show-labels="false" :multiple="false"></multiselect>
                </fieldset>
              </div>
              <div class="chart-event-header-menu-link">
                <ul id="chart-event-header-nav-tab" style="justify-content: space-evenly;" class="chart-event-tab-nav nav nav-tabs d-flex flex-row flex-nowrap" role="tablist">
                  <li class="chart-event-tab-nav-item nav-item">
                    <a class="chart-event-tab-link active" id="single-tab" data-toggle="tab" href="#single" role="tab" aria-controls="single-tab" aria-selected="true">SINGLE</a>
                  </li>
                  <li class="chart-event-tab-nav-item nav-item">
                    <a class="chart-event-tab-link" id="patti-tab" data-toggle="tab" href="#patti" role="tab" aria-controls="patti-tab" aria-selected="false">PATTI</a>
                  </li>
                  <li class="chart-event-tab-nav-item nav-item">
                    <a class="chart-event-tab-link" id="jodi-tab" data-toggle="tab" href="#jodi" role="tab" aria-controls="jodi" aria-selected="false">JODI</a>
                  </li>
                  <li class="chart-event-tab-nav-item nav-item">
                    <input type="text" class="form-control" v-model="search_odds" placeholder="Search Number">
                  </li>
                </ul>
                <div id="chart-event-header-tab-content" class="chart-event-tab-content tab-content" style="padding: 10px;">
                  <div class="tab-pane fade show active" id="single" role="tabpanel" aria-labelledby="single-tab">
                    <masonry
                      :cols="{default: 12, 1000: 6, 700: 5, 400: 3}"
                      :gutter="10"
                      >
                      <div v-for="(item, index) in single_data" class="worli-market-data" :key="index" v-if="item.odd.search(search_odds) != -1 || search_odds == ''">
                        <h4>{{item.odd}}</h4>
                        <h6 :class="item.pl < 0 ? 'text-success' : (item.pl > 0 ? 'text-danger' : '')">{{item.pl | positive}}</h6>
                      </div>
                    </masonry>
                  </div>
                  <div class="tab-pane fade " id="patti" role="tabpanel" aria-labelledby="patti-tab">
                    <!-- <masonry
                      :cols="{default: 1, 1000: 6, 700: 5, 400: 3}"
                      :gutter="10"
                      > -->
                      <div class="worli-market-data" >
                        <ul id="chart-event-header-nav-tab" class="chart-event-tab-nav nav nav-tabs d-flex flex-row flex-nowrap collapsed" data-toggle="collapse" href="#singlepatti" role="button" aria-expanded="true"  aria-controls="singlepatti">
                            <li class="chart-event-tab-nav-item nav-item"  id="singlepatti-tab" >
                                <a class="chart-event-tab-link"
                                         v-if="selected_matka_type.slug != 'khado' && selected_matka_type.slug != 'meter' && selected_matka_type.slug != 'up_down'">SINGLE PATTI</a>
                            </li>
                        </ul>
                      </div>
                      <div class="collapse show" id="singlepatti"  style="padding: 10px;">
                            <masonry
                                :cols="{default: 12, 1000: 6, 700: 5, 400: 3}"
                                :gutter="10"
                                >
                                <div v-for="(item, index) in single_patti_data" class="worli-market-data" :key="index" v-if="item.odd.search(search_odds) != -1 || search_odds == ''">
                                  <h4>{{item.odd}}</h4>
                                  <h6 :class="item.pl < 0 ? 'text-success' : (item.pl > 0 ? 'text-danger' : '')">{{item.pl | positive}}</h6>
                                </div>
                            </masonry>
                        </div>
                      <div class="worli-market-data" >
                        <ul id="chart-event-header-nav-tab" class="chart-event-tab-nav nav nav-tabs d-flex flex-row flex-nowrap collapsed" data-toggle="collapse" href="#doublepatti" role="button"  aria-expanded="true" aria-controls="doublepatti">
                            <li class="chart-event-tab-nav-item nav-item" id="doublepatti-tab" >
                                <a class="chart-event-tab-link"
                                v-if="selected_matka_type.slug != 'khado' && selected_matka_type.slug != 'meter' && selected_matka_type.slug != 'up_down'" >DOUBLE PATTI</a>
                            </li>
                        </ul>
                      </div>
                      <div class="collapse show" id="doublepatti" style="padding: 10px;">
                        <masonry
                          :cols="{default: 12, 1000: 6, 700: 5, 400: 3}"
                          :gutter="10"
                          >
                          <div v-for="(item, index) in double_patti_data" class="worli-market-data" :key="index" v-if="item.odd.search(search_odds) != -1 || search_odds == ''">
                            <h4>{{item.odd}}</h4>
                            <h6 :class="item.pl < 0 ? 'text-success' : (item.pl > 0 ? 'text-danger' : '')">{{item.pl | positive}}</h6>
                          </div>
                        </masonry>
                      </div>
                      <div class="worli-market-data" >
                        <ul id="chart-event-header-nav-tab" class="chart-event-tab-nav nav nav-tabs d-flex flex-row flex-nowrap collapsed"  data-toggle="collapse" href="#triplepatti" role="button" aria-controls="triplepatti" aria-expanded="true">
                            <li class="chart-event-tab-nav-item nav-item" id="triplepatti-tab" >
                                <a class="chart-event-tab-link"
                                v-if="selected_matka_type.slug != 'khado' && selected_matka_type.slug != 'meter' && selected_matka_type.slug != 'up_down'">TRIPLE PATTI</a>
                            </li>
                        </ul>
                      </div>
                        <div class="collapse show" id="triplepatti" style="padding: 10px;">
                            <masonry
                              :cols="{default: 12, 1000: 6, 700: 5, 400: 3}"
                              :gutter="10"
                              >
                              <div v-for="(item, index) in triple_patti_data" class="worli-market-data" :key="index" v-if="item.odd.search(search_odds) != -1  || search_odds == ''">
                                <h4>{{item.odd}}</h4>
                                <h6 :class="item.pl < 0 ? 'text-success' : (item.pl > 0 ? 'text-danger' : '')">{{item.pl | positive}}</h6>
                              </div>
                            </masonry>
                        </div>
                    <!-- </masonry> -->
                  </div>
                  <div class="tab-pane fade" id="jodi" role="tabpanel" aria-labelledby="jodi-tab" style="padding: 10px;">
                    <masonry
                      :cols="{default: 12, 1000: 6, 700: 5, 400: 3}"
                      :gutter="10"
                      >
                      <div v-for="(item, index) in jodi_data" class="worli-market-data" :key="index" v-if="item.odd.search(search_odds) != -1 || search_odds == ''">
                        <h4>{{item.odd}}</h4>
                        <h6 :class="item.pl < 0 ? 'text-success' : (item.pl > 0 ? 'text-danger' : '')">{{item.pl | positive}}</h6>
                      </div>
                    </masonry>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="content-body-sidebar content-body-sidebar-no-scroll">
        <div class="content-body-sidebar-block">
          <div class="section-heading-block">
            <h5 class="section-heading">BETS &nbsp; <span class="text-info" v-if="bets.length > 0">{{bets.length}}</span></h5>
          </div>
          <div class="sidebar-info-block-outer pad-0" style="max-height: 200px; overflow-x: auto; overflow-y: auto;">
            <table class="table">
              <thead class="thead">
                <tr>
                  <th class="text-left" v-if="auth != null && auth._role != '' && auth._role != 'is_user'">USER</th>
                  <th class="text-left">MARKET TYPE</th>
                  <th class="text-left">TYPE</th>
                  <th class="text-left">SELECTION</th>
                  <th class="text-left">ODDS</th>
                  <th class="text-left">STAKE</th>
                  <th class="text-left">P &amp; L</th>
                  <th class="text-left">DATE</th>
                  <th class="text-left" v-if="auth != null && auth._role != '' && auth._role != 'is_user' && auth._role != 'is_master'">PARENT</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(bet, index) in bets">
                  <th class="text-left pos-rel" v-if="auth != null && auth._role != '' && auth._role == 'is_director'">
                    <a href="javascript:void(0)" @click="showDeleteBet(bet.id)">
                      <i class="material-icons" style="color: red; font-size: 16px; position: absolute;">delete</i>
                    </a>
                    <span style="margin-left: 22px;">{{bet.userid}}</span>
                  </th>
                  <th class="text-left" v-else-if="auth != null && auth._role != '' && auth._role != 'is_user'">{{bet.userid}}</th>
                  <th class="text-left">{{bet.worli_matka_type.replace('_', ' ').toUpperCase()}}</th>
                  <th class="text-left">{{bet.type.toUpperCase()}}</th>
                  <th class="text-left">{{bet.selection}}
                    <span class="badge badge-primary badge-light" v-if="bet.selection_type != 'lay' && bet.worli_matka_type != 'meter'">{{bet.selection_type.toUpperCase()}}</span>
                    <span class="badge badge-danger badge-light" v-if="bet.selection_type == 'lay'">LAY</span>
                  </th>
                  <th class="text-left text-primary">{{bet.odds.replace('_', ' ').toUpperCase()}}</th>
                  <th class="text-left">{{bet.stake | positive | addCommas}}</th>
                  <th class="text-left" :class="{ 'text-danger-2' : (bet.loss != 0), 'text-success' : (bet.profit != 0)}">{{(bet.p_l) | positive | addCommas}}</th>
                  <th class="text-left">{{bet.created_at}}</th>
                  <th class="text-left" v-if="auth != null && auth._role != '' && auth._role != 'is_user' && auth._role != 'is_master'">{{bet.client_parent_userid}}</th>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
        <div class="content-body-sidebar-block clients-main-block" v-if="current_user != null && current_user.role != null && current_user.role.role != 'is_user'">
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
                </tr>
              </thead>
              <tbody v-if="clients.length > 0">
                <tr v-for="(client, index) in clients" :key="index">
                  <th>
                    <a @click.prevent="getChildBooks(client.id)" v-if="client.role_id != 5" class="text-primary" href="javascript:void(0)">{{client.userid.toUpperCase()}}</a>
                    <div v-if="client.role_id == 5">{{client.userid.toUpperCase()}}</div>
                  </th>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>
    <!-- Delete Modal -->
    <modal name="delete-modal" transition="nice-modal-fade" :scrollable="true" :draggable="true" :adaptive="true" max-width="100%" height="auto" :max-width="300" :min-width="100" :max-height="1000">
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
    props: ['matka_slug'],
    data() {
      return {
        containerId: 'matka-id',
        all_types: [
          { name: 'OPEN' },
          { name: 'CLOSE' }
        ],
        all_matka_types: [
          { name: 'REAL WORLI', slug: 'real_worli' },
          { name: 'UP AND DOWN MARKET', slug: 'up_down' },
          { name: 'EVEN ODD', slug: 'even_odd' },
          { name: 'KHADO MARKET', slug: 'khado' },
          { name: 'METER MARKET', slug: 'meter' },
          { name: 'ALL', slug: 'all' },
        ],
        selected_type: { name: 'OPEN' },
        selected_matka_type: { name: 'REAL WORLI', slug: 'real_worli' },
        current_market_title: '',
        parse_worli_date: '',
        current_user: null,
        is_loading: false,
        matka: null,
        clients: [],
        mainMarketsData: null,
        bets: [],
        currentBets: [],
        init: true,
        is_current_position: true,
        single_data: [],
        single_patti_data: [],
        double_patti_data: [],
        triple_patti_data: [],
        jodi_data: [],
        stop: false,
        search_odds: '',
        bet_delete_status:'',
      }
    },
    computed: {
      ...mapState([
        'loggedIn', 'userData', 'auth', 'origin_path', 'mobileDevice'
      ])
    },
    filters: {
      positive: function (val) {
        if (val < 0) {
          return val * -1;
        } else {
          return val;
        }
      },
      addCommas: function (val) {
        var total = val;
        if (val != '' && val != null && val != '-') {
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
      selected_type: function (val) {
        this.is_loading = true;
        this.getData();
      },
      selected_matka_type: function (val) {
        this.is_loading = true;
        this.getData();
      },
      auth: function (val) {
        if (val != null && val._role != '' && this.init) {
          this.current_user = val;
          this.init = false;
        }
      }
    },
    async created() {
      $('body').removeClass('event-page');
      await this.getMatka();
    },
    methods: {
        async getDataWhenPlacedBet() {
            await Echo.channel("refesh_my_market." + this.auth.id).listen("BroadcastBetPlacedEvent", (data) => {
                if(data.data == 'worli') {
                    this.getData();
                }
            });
        },
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
                        this.$toasted.error(res.data.error, {icon: 'not_interested'});
                    } else {
                        this.selected_bet_id = null;
                        this.$toasted.show('BET '+ this.bet_delete_status.toString() + 'ED' +' SUCCESSFULLY', {icon: 'check_circle'});
                        this.getData();
                    }
                }).catch(e => {
                    this.$toasted.error('Plsed try again!', {icon: 'not_interested'});
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
            });
        },
        async getData() {
            if (this.is_current_position) {
                await axios.get('/get-worli-matka-market-slug/'+this.$props.matka_slug+'/'+this.selected_type.name.toString()+'/'+this.selected_matka_type.slug.toString()).then(res => {
                    this.current_user = this.auth;
                    this.bets = res.data.all_bets;
                    this.single_data = res.data.single_data;
                    this.single_patti_data = res.data.single_patti_data;
                    this.double_patti_data = res.data.double_patti_data;
                    this.triple_patti_data = res.data.triple_patti_data;
                    this.jodi_data = res.data.jodi_data;
                    this.clients = res.data.clients;
                    if (res.data.type == this.selected_type.name.toString()) {
                        this.is_loading = false;
                    }
                    this.getDataWhenPlacedBet()
                }).catch(e => {
                    this.$toasted.error('Please refresh page !', {icon: 'not_interested'});
                    console.log(e.response);
                });
            } else {
                this.getChildBooks(this.current_user.id);
            }
        },
        async getChildBooks(user_id) {
            this.is_current_position = false;
            axios.get('/get-worli-matka-child-market-slug/'+this.$props.matka_slug+'/'+this.selected_type.name.toString()+'/'+this.selected_matka_type.slug.toString()+'/'+user_id).then(res => {
                this.current_user = res.data.current_user;
                this.bets = res.data.all_bets;
                this.single_data = res.data.single_data;
                this.single_patti_data = res.data.single_patti_data;
                this.double_patti_data = res.data.double_patti_data;
                this.triple_patti_data = res.data.triple_patti_data;
                this.jodi_data = res.data.jodi_data;
                this.clients = res.data.clients;
                if (res.data.type == this.selected_type.name.toString()) {
                    this.is_loading = false;
                }
            }).catch(e => {
                this.$toasted.error('Please refresh page !', {icon: 'not_interested'});
            });
        },
        getPreviousData() {
            var id = this.auth.id;
            if (this.auth._role == 'is_manager') {
                id = 1;
            }
            if ((this.current_user != null && this.current_user != '') && this.current_user.parent_id === id) {
                this.is_current_position = true;
                this.getData();
            } else if (this.current_user != null && this.current_user != '') {
                this.getChildBooks(this.current_user.client_parent_id);
            }
        },
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
