<template>
  <div class="content-body-outer my-market-event-detail-page" v-if="event != null">
    <div class="transaction-history-nav-outer">
      <ul class="transaction-history-nav">
        <li>
          <a href="javascript:void(0)">
            <span class="text-danger-2">{{event.event_name.toUpperCase()}}</span> &nbsp; PROFIT &amp; LOSS DETAILS
          </a>
        </li>
      </ul>
    </div>
    <div v-if="is_loading" style="position: relative; min-height: 400px">
      <loader></loader>
    </div>
    <div v-else class="d-lg-flex flex-row event-detail-page-block">
      <div class="content-body-main-block">
        <div class="chart-main-block-outer-single">
          <div class="chart-main-block">
            <div class="chart-event-header-outer">
              <div class="chart-event-title-block-outer d-flex flex-row" v-if="event != null">
                <div class="chart-event-time-block mr-3">
                  <h6 class="chart-event-time-heading">{{event.date}}</h6>
                  <div class="chart-event-time">{{event.time}}</div>
                </div>
                <div class="chart-event-title-block">
                  <h3 class="chart-event-title">{{event.event_name}}</h3>
                </div>
              </div>
              <div class="chart-event-header-menu-link">
                <ul id="chart-event-header-nav-tab" class="chart-event-tab-nav nav nav-tabs d-flex flex-row flex-nowrap" role="tablist">
                  <li class="chart-event-tab-nav-item nav-item">
                    <a class="chart-event-tab-link active" id="all-tab" data-toggle="tab" href="#all" role="tab" aria-controls="all" aria-selected="true">ALL</a>
                  </li>
                  <li class="chart-event-tab-nav-item nav-item">
                    <a class="chart-event-tab-link" id="exchange-tab" data-toggle="tab" href="#exchange" role="tab" aria-controls="exchange" aria-selected="false">MATCH ODDS</a>
                  </li>
                  <li class="chart-event-tab-nav-item nav-item">
                    <a class="chart-event-tab-link" id="bookmaker-tab" data-toggle="tab" href="#bookmaker" role="tab" aria-controls="bookmaker" aria-selected="false">BOOKMAKER</a>
                  </li>
                  <li class="chart-event-tab-nav-item nav-item">
                    <a class="chart-event-tab-link" id="fancy-tab" data-toggle="tab" href="#fancy" role="tab" aria-controls="fancy" aria-selected="false">FANCY</a>
                  </li>
                  <li class="chart-event-tab-nav-item nav-item">
                    <a class="chart-event-tab-link" id="other-tab" data-toggle="tab" href="#other" role="tab" aria-controls="other" aria-selected="false">OTHER</a>
                  </li>
                </ul>
                <div id="chart-event-header-tab-content" class="chart-event-tab-content tab-content">
                  <div class="tab-pane fade show active" id="all" role="tabpanel" aria-labelledby="all-tab">
                    <!-- All Data -->
                    <div class="profit-loss-main-data-block table-responsive" v-if="current_user != null">
                      <table class="table">
                        <thead class="thead">
                          <tr>
                            <th>MARKET</th>
                            <th>SELECTION</th>
                            <th>P &amp; L</th>
                            <th>RESULT</th>
                            <th>BET COUNT</th>
                            <th>SHOW BETS</th>
                          </tr>
                        </thead>
                        <tbody>
                          <!-- Main Markets Data -->
                          <tr v-if="mainMarketsData != null && mainMarketsData.length > 0" v-for="data in mainMarketsData">
                            <td>MAIN MARKETS</td>
                            <td>{{data.name}}</td>
                            <td :class="{ 'text-danger-2' : (data.p_l > 0), 'text-success' : (data.p_l < 0)}"><b>{{data.p_l | positive | addCommas}}</b></td>
                            <td>{{data.result}}</td>
                            <td>{{data.bet_count}}</td>
                            <td width="5%"><a href="javascript:void(0)"><i class="material-icons" style="font-size: 18px;" @click="getMarketBets(event.id,'Main Markets', data.match_market_id,data.name)">open_in_new</i></a></td>
                          </tr>
                          <!-- Bookmaker Data -->
                          <tr v-if="bookmakersData != null && bookmakersData.length > 0" v-for="data in bookmakersData">
                            <td>BOOKMAKER</td>
                            <td>{{data.name}}</td>
                            <td :class="{ 'text-danger-2' : (data.p_l > 0), 'text-success' : (data.p_l < 0)}"><b>{{data.p_l | positive | addCommas}}</b></td>
                            <td>{{data.result}}</td>
                            <td>{{data.bet_count}}</td>
                            <td width="5%"><a href="javascript:void(0)"><i class="material-icons" style="font-size: 18px;" @click="getMarketBets(event.id,'Bookmaker', data.match_bookmaker_id,data.name)">open_in_new</i></a></td>
                          </tr>
                          <!-- Fancy Data -->
                          <tr v-if="fanciesData != null && fanciesData.length > 0" v-for="data in fanciesData">
                            <td>FANCY</td>
                            <td>{{data.name}}</td>
                            <td :class="{ 'text-danger-2' : (data.p_l > 0), 'text-success' : (data.p_l < 0)}"><b>{{data.p_l | positive | addCommas}}</b></td>
                            <td>{{data.result}}</td>
                            <td>{{data.bet_count}}</td>
                            <td width="5%"><a href="javascript:void(0)"><i class="material-icons" style="font-size: 18px;" @click="getMarketBets(event.id,'Fancy', data.match_fancy_id,data.name)">open_in_new</i></a></td>
                          </tr>
                        </tbody>
                        <tfoot class="thead">
                          <th>TOTAL</th>
                          <th></th>
                          <th :class="{ 'text-danger-2' : (allTotalPl > 0), 'text-success' : (allTotalPl < 0)}"><b>{{allTotalPl | positive | addCommas}}</b></th>
                          <th></th>
                          <th></th>
                        </tfoot>
                      </table>
                    </div>
                  </div>
                  <div class="tab-pane fade" id="exchange" role="tabpanel" aria-labelledby="exchange-tab">
                    <!-- Exchange Data -->
                    <div class="profit-loss-main-data-block table-responsive" v-if="current_user != null">
                      <table class="table">
                        <thead class="thead">
                          <tr>
                            <th>MARKET</th>
                            <th>P &amp; L</th>
                            <th>RESULT</th>
                            <th>BET COUNT</th>
                            <th>SHOW BETS</th>
                          </tr>
                        </thead>
                        <tbody>
                          <tr v-if="mainMarketsData != null && mainMarketsData.length > 0 && data.name == 'Match Odds'" v-for="data in mainMarketsData">
                            <td>{{data.name}}</td>
                            <td :class="{ 'text-danger-2' : (data.p_l > 0), 'text-success' : (data.p_l < 0)}"><b>{{data.p_l | positive | addCommas}}</b></td>
                            <td>{{data.result}}</td>
                            <td>{{data.bet_count}}</td>
                            <td width="5%"><a href="javascript:void(0)"><i class="material-icons" style="font-size: 18px;" @click="getMarketBets(event.id,'Main Markets', data.match_market_id,data.name)">open_in_new</i></a></td>
                          </tr>
                        </tbody>
                      </table>
                    </div>
                  </div>
                  <div class="tab-pane fade" id="bookmaker" role="tabpanel" aria-labelledby="bookmaker-tab">
                    <!-- Bookmakers Data -->
                    <div class="profit-loss-main-data-block table-responsive" v-if="current_user != null">
                      <table class="table">
                        <thead class="thead">
                          <tr>
                            <th>MARKET</th>
                            <th>P &amp; L</th>
                            <th>RESULT</th>
                            <th>BET COUNT</th>
                            <th>SHOW BETS</th>
                          </tr>
                        </thead>
                        <tbody>
                          <tr v-if="bookmakersData != null && bookmakersData.length > 0" v-for="data in bookmakersData">
                            <td>{{data.name}}</td>
                            <td :class="{ 'text-danger-2' : (data.p_l > 0), 'text-success' : (data.p_l < 0)}"><b>{{data.p_l | positive | addCommas}}</b></td>
                            <td>{{data.result}}</td>
                            <td>{{data.bet_count}}</td>
                            <td width="5%"><a href="javascript:void(0)"><i class="material-icons" style="font-size: 18px;" @click="getMarketBets(event.id,'Bookmaker', data.match_bookmaker_id,data.name)">open_in_new</i></a></td>
                          </tr>
                        </tbody>
                        <tfoot class="thead">
                          <th>TOTAL</th>
                          <th :class="{ 'text-danger-2' : (bookmakerTotalTotal > 0), 'text-success' : (bookmakerTotalTotal < 0)}"><b>{{bookmakerTotalTotal | positive | addCommas}}</b></th>
                          <th></th>
                          <th></th>
                        </tfoot>
                      </table>
                    </div>
                  </div>
                  <div class="tab-pane fade" id="fancy" role="tabpanel" aria-labelledby="fancy-tab">
                    <!-- Fancy Data -->
                    <div class="profit-loss-main-data-block table-responsive" v-if="current_user != null">
                      <table class="table">
                        <thead class="thead">
                          <tr>
                            <th>MARKET</th>
                            <th>P &amp; L</th>
                            <th>RESULT</th>
                            <th>BET COUNT</th>
                            <th>SHOW BETS</th>
                          </tr>
                        </thead>
                        <tbody>
                          <tr v-if="fanciesData != null && fanciesData.length > 0" v-for="data in fanciesData">
                            <td>{{data.name}}</td>
                            <td :class="{ 'text-danger-2' : (data.p_l < 0), 'text-success' : (data.p_l > 0)}"><b>{{data.p_l | positive | addCommas}}</b></td>
                            <td>{{data.result}}</td>
                            <td>{{data.bet_count}}</td>
                            <td width="5%"><a href="javascript:void(0)"><i class="material-icons" style="font-size: 18px;" @click="getMarketBets(event.id,'Fancy', data.match_fancy_id,data.name)">open_in_new</i></a></td>
                          </tr>
                        </tbody>
                        <tfoot class="thead">
                          <th>TOTAL</th>
                          <th :class="{ 'text-danger-2' : (fancyTotalTotal < 0), 'text-success' : (fancyTotalTotal > 0)}"><b>{{fancyTotalTotal | positive | addCommas}}</b></th>
                          <th></th>
                          <th></th>
                        </tfoot>
                      </table>
                    </div>
                  </div>
                  <div class="tab-pane fade" id="other" role="tabpanel" aria-labelledby="other-tab">
                    <!-- Other Data -->
                    <div class="profit-loss-main-data-block table-responsive" v-if="current_user != null">
                      <table class="table">
                        <thead class="thead">
                          <tr>
                            <th>MARKET</th>
                            <th>P &amp; L</th>
                            <th>RESULT</th>
                            <th>BET COUNT</th>
                            <th>SHOW BETS</th>
                          </tr>
                        </thead>
                        <tbody>
                          <tr v-if="mainMarketsData != null && mainMarketsData.length > 0 && data.name != 'Match Odds'" v-for="data in mainMarketsData">
                            <td>{{data.name}}</td>
                            <td :class="{ 'text-danger-2' : (data.p_l > 0), 'text-success' : (data.p_l < 0)}"><b>{{data.p_l | positive | addCommas}}</b></td>
                            <td>{{data.result}}</td>
                            <td>{{data.bet_count}}</td>
                            <td width="5%"><a href="javascript:void(0)"><i class="material-icons" style="font-size: 18px;" @click="getMarketBets(event.id,'Main Markets', data.match_market_id,data.name)">open_in_new</i></a></td>
                          </tr>
                        </tbody>
                        <tfoot class="thead">
                          <th>TOTAL</th>
                          <th :class="{ 'text-danger-2' : (otherTotalPl > 0), 'text-success' : (otherTotalPl < 0)}"><b>{{otherTotalPl | positive | addCommas}}</b></th>
                          <th></th>
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
      </div>
      <div class="content-body-sidebar content-body-sidebar-no-scroll">
        <div class="content-body-sidebar-block clients-main-block">
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
        </div>
        <div class="modal-body">
          <div class="card-body" style="height: 100%; padding-left: 0; padding-right: 0;">
            <div v-if="currentBets.length > 0">
              <div class="vue-modal-scroll">
                <div class="table-responsive">
                  <table class="table">
                    <thead class="thead">
                      <tr>
                        <th class="text-left" v-if="auth._role != 'is_user'">User</th>
                        <th class="text-left">Selection</th>
                        <th class="text-left">Odds</th>
                        <th class="text-left">Rate</th>
                        <th class="text-left">Stake</th>
                        <th class="text-left">Date</th>
                        <th class="text-left">P &amp; L</th>
                        <th class="text-left">Commission</th>
                        <th class="text-left">Total P &amp; L</th>
                        <th class="text-left" v-if="auth != null && auth._role != 'is_user' && auth._role != 'is_master'">PARENT</th>
                        <th class="text-left" v-if="auth._role != 'is_user'">IP</th>
                      </tr>
                    </thead>
                    <tbody>
                      <tr v-for="(bet, index) in currentBets">
                        <th class="text-left pos-rel" v-if="auth._role != 'is_user'">
                          <a href="javascript:void(0)" style="margin-right: 22px;" @click="showDeleteBet(bet.id)" v-if="auth._role == 'is_director'">
                            <i class="material-icons" style="color: red; font-size: 16px; position: absolute;">delete</i>
                          </a>
                          <span>{{bet.userid}}</span>
                        </th>
                        <th class="text-left">{{bet.selection}}
                          <span class="badge badge-primary badge-light" v-if="bet.selection_type == 'back'">BACK</span>
                          <span class="badge badge-danger badge-light" v-if="bet.selection_type == 'lay'">LAY</span>
                        </th>
                        <th class="text-left text-primary">{{bet.odds}}</th>
                        <td class="text-left">{{bet.bet_market_type == 3 && bet.fancy_rate != 0 ? bet.fancy_rate : ''}}</td>
                        <th class="text-left">{{bet.stake | positive | addCommas}}</th>
                        <th class="text-left">{{bet.created_at}}</th>
                        <th class="text-left" :class="{ 'text-danger-2' : (bet.loss != 0), 'text-success' : (bet.profit != 0)}">{{(bet.profit != 0 ? (bet.profit - bet.commission) : (bet.loss - bet.commission)) | positive | addCommas}}</th>
                        <th class="text-left text-primary">{{(bet.commission != null ? bet.commission : 0) | positive | addCommas}}</th>
                        <th class="text-left" :class="{ 'text-danger-2' : (bet.loss != 0), 'text-success' : (bet.profit != 0)}">{{(bet.profit != 0 ? bet.profit : bet.loss) | positive | addCommas}}</th>
                        <th class="text-left" v-if="auth != null && auth._role != 'is_user' && auth._role != 'is_master'">{{bet.parent_user_id}}</th>
                        <th class="text-left text-primary" v-if="auth._role != 'is_user'">{{bet.ip}}</th>
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
    <modal name="delete-bet-reason-modal" 
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
    props: ['event_slug'],
    data() {
      return {
        current_market_title: '',
        current_user: '',
        is_current_position: true,
        is_loading: false,
        event: null,
        clients: [],
        mainMarketsData: [],
        exchangeData: [],
        exchangeBets: [],
        bookmakersData: [],
        bookmakersBets: [],
        fanciesData: [],
        fanciesBets: [],
        currentBets: [],
        init: true,
        is_current_position: true,
        bet_delete_status:'',
      }
    },
    computed: {
      allTotalPl() {
        var pl = 0;
        for (var i = this.mainMarketsData.length - 1; i >= 0; i--) {
          pl = (pl + this.mainMarketsData[i].p_l);
        }
        for (var i = this.bookmakersData.length - 1; i >= 0; i--) {
          pl = (pl + this.bookmakersData[i].p_l) - this.bookmakersData[i].commission;
        }
        for (var i = this.fanciesData.length - 1; i >= 0; i--) {
          pl = (pl + this.fanciesData[i].p_l) - this.fanciesData[i].commission;
        }
        return pl;
      },
      allTotalCommission() {
        var commission = 0;
        for (var i = this.bookmakersData.length - 1; i >= 0; i--) {
          commission = parseFloat(commission) + parseFloat(this.bookmakersData[i].commission);
        }
        for (var i = this.fanciesData.length - 1; i >= 0; i--) {
          commission = parseFloat(commission) + parseFloat(this.fanciesData[i].commission);
        }
        return commission;
      },
      allTotalTotal() {
        var total = 0;
        for (var i = this.mainMarketsData.length - 1; i >= 0; i--) {
          total = total + this.mainMarketsData[i].p_l;
        }
        for (var i = this.bookmakersData.length - 1; i >= 0; i--) {
          total = total + this.bookmakersData[i].p_l;
        }
        for (var i = this.fanciesData.length - 1; i >= 0; i--) {
          total = total + this.fanciesData[i].p_l;
        }
        return total;
      },
      bookmakerTotalPl() {
        var pl = 0;
        for (var i = this.bookmakersData.length - 1; i >= 0; i--) {
          pl = (pl + this.bookmakersData[i].p_l) - this.bookmakersData[i].commission;
        }
        return pl;
      },
      bookmakerTotalCommission() {
        var commission = 0;
        for (var i = this.bookmakersData.length - 1; i >= 0; i--) {
          commission = commission + this.bookmakersData[i].commission;
        }
        return commission;
      },
      bookmakerTotalTotal() {
        var total = 0;
        for (var i = this.bookmakersData.length - 1; i >= 0; i--) {
          total = total + this.bookmakersData[i].p_l;
        }
        return total;
      },
      fancyTotalPl() {
        var pl = 0;
        for (var i = this.fanciesData.length - 1; i >= 0; i--) {
          pl = (pl + this.fanciesData[i].p_l) - this.fanciesData[i].commission;
        }
        return pl;
      },
      fancyTotalCommission() {
        var commission = 0;
        for (var i = this.fanciesData.length - 1; i >= 0; i--) {
          commission = commission + this.fanciesData[i].commission;
        }
        return commission;
      },
      fancyTotalTotal() {
        var total = 0;
        for (var i = this.fanciesData.length - 1; i >= 0; i--) {
          total = total + this.fanciesData[i].p_l;
        }
        return total;
      },
      otherTotalPl() {
        var total = 0;
        for (var i = this.mainMarketsData.length - 1; i >= 0; i--) {
          if (this.mainMarketsData[i].name != 'Match Odds') {
            total = total + this.mainMarketsData[i].p_l;
          }
        }
        return total;
      },
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
      await this.getEvent();
      if ($(window).width() < 1280) {
        $('.event-detail-page-block').removeClass('flex-row');
        $('.event-detail-page-block').addClass('flex-column');
      }
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
                axios.post('/delete-bet', {id: this.selected_bet_id, type: this.bet_delete_status.toString(), 'reason':reason}).then(res => {
                    if (res.data.error != null) {
                        this.$toasted.error(res.data.error, {icon: 'not_interested'});
                    } else {
                        this.currentBets = this.currentBets.filter((e)=>e.id !== this.selected_bet_id );
                        this.selected_bet_id = null;
                        this.$toasted.show('BET '+ this.bet_delete_status.toString() + 'ED' +' SUCCESSFULLY', {icon: 'check_circle'});
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
      async getEvent() {
        await axios.get('/get-event-for-profit-loss-by-slug/'+this.$props.event_slug).then(res => {
          if (res.data.event == null) {
            window.location.href = window.location.origin;
          } else {
            this.event = res.data.event;
            this.getData();
          }
        }).catch(e => {
          this.getEvent();
          console.log(e.response);
        });
      },
      async getData() {
        this.is_loading = true;
        await axios.get('/get-event-profit-loss-data-by-slug/'+this.$props.event_slug).then(res => {
          this.current_user = this.auth;
          this.mainMarketsData = res.data.data.mainMarketsData;
          this.bookmakersData = res.data.data.bookmakersData;
          this.fanciesData = res.data.data.fanciesData;
          this.clients = res.data.clients;
        }).catch(e => {
          console.log(e.response);
        });
        this.is_loading = false;
      },
      async showClientsData(id) {
        this.is_current_position = false;
        this.is_loading = true;
        await axios.get('/get-event-profit-loss-data-by-match-slug-and-client-user-id/'+this.$props.event_slug+'/'+id).then(res => {
          this.current_user = res.data.current_user;
          this.mainMarketsData = res.data.data.mainMarketsData;
          this.bookmakersData = res.data.data.bookmakersData;
          this.fanciesData = res.data.data.fanciesData;
          this.clients = res.data.clients;
        }).catch(e => {
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
      getMarketBets(event_id, type, type_data_id, type_data_name) {
        let current_user_id = this.current_user.id;
        this.is_loading = true;
        axios.get('/get-event-all-bet-data-by-match-id-and-market-type-and-client-user-id/'+current_user_id+'/'+event_id+'/'+type+'/'+type_data_id).then(res => {
          this.current_market_title = type_data_name;
          this.currentBets = res.data.currentBets;
          this.$modal.show('bets-modal');
          this.is_loading = false;
        }).catch(e => {
          this.is_loading = false;
          this.$toasted.error('Plsed try again!', {icon: 'not_interested'});
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
