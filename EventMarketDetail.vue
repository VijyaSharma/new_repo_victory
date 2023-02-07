<template>
  <div class="content-body-outer my-market-event-detail-page" v-if="event != null">
    <div class="transaction-history-nav-outer">
      <ul class="transaction-history-nav">
        <li>
          <a href="javascript:void(0)">
            <span class="text-danger-2">{{ event.event_name.toUpperCase() }}</span>
            &nbsp; FULL MARKET DETAILS
          </a>
        </li>
      </ul>

    </div>
    <div v-if="is_loading" style="position: relative; min-height: 400px">
      <loader></loader>
    </div>
    <div v-else-if="event != null" class="d-lg-flex flex-row event-detail-page-block">
      <div class="content-body-main-block">
        <div class="live-score-mobile mb-1 hide-on-medium">
          <div class="panel with-nav-tabs panel-mobile">
            <div class="panel-heading">
              <div class="section-heading-block">
                <ul class="nav nav-tabs" style="border: none">
                  <li class="active live-score-board-tab">
                    <a href="#showLiveScore" data-toggle="tab" @click="collapsePanel('scorecard')"><h5 class="section-heading">Live Score</h5></a>
                  </li>
                  <li class="live-score-board-tab" style="margin-left: 15px">
                    <a href="#tab2Mobile" @click="collapsePanel('tv')" data-toggle="tab"><h5 class="section-heading">Live TV</h5></a>
                  </li>
                  <li style="margin-left: auto; font-size: 22px">
                    <i class="fa fa-angle-down" @click="collapsePanel('scorecard')" data-toggle="collapse" aria-expanded="false" aria-controls="panel-score-collapse" data-target="#panel-score-collapse" aria-hidden="true"></i>
                  </li>
                </ul>
              </div>
            </div>
            <div class="panel-body collapse-in collapse show" id="panel-score-collapse">
              <div class="tab-content">
                <div class="tab-pane fade in active show" id="showLiveScore">
                  <div class="live-score-content">
                    <div class="widgets">
                        <div v-if="logo == 'bullexch' || logo == 'betdaily' || logo == 'mmo' || logo == 'hdexch444'"><div class="sr-widget sr-widget-2"></div></div>
                    </div>
                  </div>
                </div>
                <div class="tab-pane fade" id="tab2Mobile" >
                    <div v-if="auth._role != '' && mobileDevice && tab_opened == 'tv' && !event.is_custom">
                        <iframe :src='"https://vid.dreamcasino.live/GetAPI.html?MatchID=" + event.event_id' scrolling="no" frameborder="0" :style="mobileDevice ? 'height: 230px !important; width: 100%;' : 'height: 235px !important; width: 100%;'"></iframe>
                    </div>
                    <div v-if="auth._role != '' && mobileDevice && tab_opened == 'tv' && event.is_custom">
                        <iframe :src='"https://vid.dreamcasino.live/GetAPI.html?MatchID=" + event.tv_url' scrolling="no" frameborder="0" :style="mobileDevice ? 'height: 230px !important; width: 100%;' : 'height: 235px !important; width: 100%;'"></iframe>
                    </div>
                </div>
                <div class="tab-pane fade" id="tab3default">
                  <div id="accordion">
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div>
          <div class="chart-main-block-outer-single">
            <div class="chart-main-block">
              <div class="chart-event-header-outer">
                <div :class="event.inplay ? 'chart-event-title-block-outer d-flex flex-row in-play-event' : 'chart-event-title-block-outer d-flex flex-row'" v-if="event != null">
                  <div class="chart-event-time-block mr-3">
                    <h6 class="chart-event-time-heading" v-if="event.inplay">INPLAY</h6>
                    <h6 class="chart-event-time-heading" v-else>
                      {{ event.date }}
                    </h6>
                    <div class="chart-event-time">{{ event.time }}</div>
                  </div>
                  <div class="chart-event-title-block">
                    <h3 class="chart-event-title">{{ event.event_name }}</h3>
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
                      <!-- Other And Match Odds Markets -->
                      <div class="chart-body-outer" v-if="market.is_active && market.is_declared == 0 && market.market_name == 'Match Odds'" v-for="(market, idx) in markets">
                        <div class="chart-body-heading-block">
                          <div class="chart-body-heading">
                            <div class="d-flex justify-content-between flex-row">
                              <h6 class="chart-body-heading chart-body-main-heading pt-1">
                                <span>{{ market.market_name }}</span>
                              </h6>
                              <div class="chart-body-heading-details">
                                <div class="d-flex flex-row justify-content-between">
                                  <div class="chart-body-heading chart-body-heading-outer d-flex flex-row justify-content-end">
                                    <div class="chart-body-heading-two">
                                      <button href="javascript:void(0)" style="font-size: 13px" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default book-btn" title="Booking"  @click="openBookOrBetModal(market.id, 'M', market.market_name, '', 'book-bookmaker-market-modal', true)">Book</button>
                                    </div>
                                    <div class="chart-body-heading-two">
                                      <button href="javascript:void(0)" style="font-size: 13px; background: #4f687d" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default bet-btn" title="Bets"  @click="openBookOrBetModal(market.id, 'BM', market.market_name, '', 'bets-fancy-bookmaker-market-modal', true)">Bets</button>
                                    </div>
                                  </div>
                                </div>
                              </div>
                            </div>
                          </div>
                        </div>
                        <div class="chart-body-heading-block">
                          <div class="chart-body-heading">
                            <div class="d-flex justify-content-between flex-row">
                              <h6 class="chart-body-heading chart-body-main-heading">
                                <span style="display: none">{{ market.market_name }}</span>
                              </h6>
                              <div class="chart-body-heading-details">
                                <div class="d-flex flex-row justify-content-between">
                                  <div class="chart-body-heading chart-body-heading-outer d-flex flex-row justify-content-end">
                                    <div class="chart-body-heading-two">
                                      <span>Back</span>
                                    </div>
                                  </div>
                                  <div class="chart-body-heading chart-body-heading-outer-two chart-body-heading-outer d-flex flex-row">
                                    <div class="chart-body-heading-one">
                                      <span>Lay</span>
                                    </div>
                                  </div>
                                </div>
                              </div>
                            </div>
                          </div>
                        </div>
                        <div :class="event.is_sus || event.is_active == 0 || market.is_sus || market.data == null ? 'chart-body suspended' : 'chart-body'" data-content="suspended">
                          <ul v-if="event.is_sus || event.is_active == 0 || market.is_sus || market.data == null">
                            <li v-for="(runner, rIndex) in market.selection_runner">
                              <div class="match-odds-outer d-flex justify-content-between flex-row">
                                <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                  <div class="match-odds-title-block">
                                    <h5 class="match-odds-title">
                                      {{ runner.runner_name }}
                                    </h5>
                                    <div  v-for="(m_runner_books, runner_bookIndex) in market.runner_books"  v-if="m_runner_books.selection_id == runner.selection_id"    :class="m_runner_books.pl > 0 ? 'match-odds-tag danger' : m_runner_books.pl < 0 ? 'match-odds-tag success' : 'match-odds-tag'">
                                      <span>{{ m_runner_books.pl | positive | addCommas }}</span>
                                    </div>
                                  </div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                  <h5 class="price-odd-title"></h5>
                                  <div class="price-odd"></div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                  <h5 class="price-odd-title"></h5>
                                  <div class="price-odd"></div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                  <h5 class="price-odd-title"></h5>
                                  <div class="price-odd"></div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                  <h5 class="price-odd-title"></h5>
                                  <div class="price-odd"></div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                  <h5 class="price-odd-title"></h5>
                                  <div class="price-odd"></div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                  <h5 class="price-odd-title"></h5>
                                  <div class="price-odd"></div>
                                </a>
                              </div>
                            </li>
                            <li v-if="currentSport.stake_size != null">
                              <div class="match-odds-outer d-flex justify-content-end flex-row min-max-text" style="margin-right: 3px; margin-top: 5px">
                                Min:
                                {{ currentSport.stake_size.min_match }} |&nbsp;
                                <span v-if="event.is_custom != 1">Max: {{ event.inplay ? (market.inplay_stake_limit != null ? market.inplay_stake_limit : (event.match_setting != null && event.match_setting.is_active && (event.match_setting.exch_stake_limit != null && event.match_setting.exch_stake_limit != '') ? event.match_setting.exch_stake_limit : (market.stake_limit != null && market.stake_limit != '' ? market.stake_limit :  currentSport.stake_size.max_match))) : (event.match_setting != null && event.match_setting.is_active && (event.match_setting.exch_stake_limit != null && event.match_setting.exch_stake_limit != '') ? event.match_setting.exch_stake_limit : (market.stake_limit != null && market.stake_limit != '' ? market.stake_limit :  currentSport.stake_size.max_match)) | kiloConverter }}</span>
                                <span v-if="event.is_custom == 1"> Max: {{ event.match_setting != null && event.match_setting.is_active && (event.match_setting.exch_stake_limit != null && event.match_setting.exch_stake_limit != '') ? event.match_setting.exch_stake_limit : (market.stake_limit != null && market.stake_limit != '' ? market.stake_limit :  currentSport.stake_size.max_match) | kiloConverter }}</span>
                              </div>
                            </li>
                          </ul>
                          <ul v-else>
                            <li v-for="(runner, rIndex) in market.selection_runner">
                              <div class="match-odds-outer d-flex justify-content-between flex-row" v-for="(data_runner, data_runner_Index) in market.data.runners" v-if="data_runner.selectionId == runner.selection_id">
                                <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                  <div class="match-odds-title-block">
                                    <h5 class="match-odds-title">
                                      {{ runner.runner_name }}
                                    </h5>
                                    <div v-for="(m_runner_books, runner_bookIndex) in market.runner_books"  v-if="m_runner_books.selection_id == runner.selection_id" :class="m_runner_books.pl > 0 ? 'match-odds-tag danger' : m_runner_books.pl < 0 ? 'match-odds-tag success' : 'match-odds-tag'">
                                      <span>{{ m_runner_books.pl | positive | addCommas }}</span>
                                    </div>
                                  </div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                  <h5 class="price-odd-title">
                                    {{ isset(data_runner.ex.availableToBack[2]) ? data_runner.ex.availableToBack[2].price : "" }}
                                  </h5>
                                  <div class="price-odd">
                                    {{ isset(data_runner.ex.availableToBack[2]) ? data_runner.ex.availableToBack[2].size : "" }}
                                  </div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                  <h5 class="price-odd-title">
                                    {{ isset(data_runner.ex.availableToBack[1]) ? data_runner.ex.availableToBack[1].price : "" }}
                                  </h5>
                                  <div class="price-odd">
                                    {{ isset(data_runner.ex.availableToBack[1]) ? data_runner.ex.availableToBack[1].size : "" }}
                                  </div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                  <h5 class="price-odd-title">
                                    {{ isset(data_runner.ex.availableToBack[0]) ? data_runner.ex.availableToBack[0].price : "" }}
                                  </h5>
                                  <div class="price-odd">
                                    {{ isset(data_runner.ex.availableToBack[0]) ? data_runner.ex.availableToBack[0].size : "" }}
                                  </div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                  <h5 class="price-odd-title">
                                    {{ isset(data_runner.ex.availableToLay[0]) ? data_runner.ex.availableToLay[0].price : "" }}
                                  </h5>
                                  <div class="price-odd">
                                    {{ isset(data_runner.ex.availableToLay[0]) ? data_runner.ex.availableToLay[0].size : "" }}
                                  </div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                  <h5 class="price-odd-title">
                                    {{ isset(data_runner.ex.availableToLay[1]) ? data_runner.ex.availableToLay[1].price : "" }}
                                  </h5>
                                  <div class="price-odd">
                                    {{ isset(data_runner.ex.availableToLay[1]) ? data_runner.ex.availableToLay[1].size : "" }}
                                  </div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                  <h5 class="price-odd-title">
                                    {{ isset(data_runner.ex.availableToLay[2]) ? data_runner.ex.availableToLay[2].price : "" }}
                                  </h5>
                                  <div class="price-odd">
                                    {{ isset(data_runner.ex.availableToLay[2]) ? data_runner.ex.availableToLay[2].size : "" }}
                                  </div>
                                </a>
                              </div>
                            </li>
                            <li v-if="currentSport.stake_size != null">
                              <div class="match-odds-outer d-flex justify-content-end flex-row min-max-text" style="margin-right: 3px; margin-top: 5px">
                                Min:
                                {{ currentSport.stake_size.min_match }} |&nbsp;
                                <span v-if="event.is_custom != 1">Max: {{ event.inplay ? (market.inplay_stake_limit != null ? market.inplay_stake_limit : (event.match_setting != null && event.match_setting.is_active && (event.match_setting.exch_stake_limit != null && event.match_setting.exch_stake_limit != '') ? event.match_setting.exch_stake_limit : (market.stake_limit != null && market.stake_limit != '' ? market.stake_limit :  currentSport.stake_size.max_match))) : (event.match_setting != null && event.match_setting.is_active && (event.match_setting.exch_stake_limit != null && event.match_setting.exch_stake_limit != '') ? event.match_setting.exch_stake_limit : (market.stake_limit != null && market.stake_limit != '' ? market.stake_limit :  currentSport.stake_size.max_match)) | kiloConverter }}</span>
                                <span v-if="event.is_custom == 1"> Max: {{ event.match_setting != null && event.match_setting.is_active && (event.match_setting.exch_stake_limit != null && event.match_setting.exch_stake_limit != '') ? event.match_setting.exch_stake_limit : (market.stake_limit != null && market.stake_limit != '' ? market.stake_limit :  currentSport.stake_size.max_match) | kiloConverter }}</span>
                              </div>
                            </li>
                          </ul>
                        </div>
                      </div>
                      <!-- All Bookmakers Odds -->
                      <div class="chart-body-outer" v-if="current_bookmakers.length > 0" v-for="(bookmaker, bookIndex) in current_bookmakers" :key="bookIndex">
                        <div v-if="auth._role != 'is_director' && bookmaker.is_active">
                          <div class="chart-body-heading-block event-detail-manage-main-block">
                            <div class="event-detail-manage-block d-flex justify-content-between flex-row">
                              <h5>
                                {{ bookmaker.title }}
                                <span class="badge badge-primary">Bookmaker</span>
                              </h5>
                              <div class="chart-body-heading-details">
                                <div class="d-flex flex-row justify-content-between">
                                  <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                    <label class="button-toggle-wrap">
                                      <div class="chart-body-heading-two" style="padding-right: 0px">
                                        <button href="javascript:void(0)" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default book-btn" title="Booking" @click="openBookOrBetModal(null, 'B', bookmaker.title, '', 'book-bookmaker-market-modal', true, bookmaker.id, null)">Book</button>
                                      </div>
                                    </label>
                                  </div>

                                  <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                    <label class="button-toggle-wrap">
                                      <div class="chart-body-heading-two" style="padding-right: 0px">
                                        <button href="javascript:void(0)" style="background: #4f687d" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default bet-btn" title="Bets"  @click="openBookOrBetModal(null, 'BB', bookmaker.title, '', 'bets-fancy-bookmaker-market-modal', true, bookmaker.id)">Bets</button>
                                      </div>
                                    </label>
                                  </div>
                                </div>
                              </div>
                            </div>
                          </div>
                          <div class="chart-body-heading-block">
                            <div class="chart-body-heading">
                              <div class="d-flex justify-content-between flex-row" style="justify-content: flex-end !important">
                                <h6 class="chart-body-heading chart-body-main-heading" style="display: none">
                                  <span>{{ bookmaker.title }}</span>
                                </h6>
                                <div class="chart-body-heading-details">
                                  <div class="d-flex flex-row justify-content-between">
                                    <div class="chart-body-heading chart-body-heading-outer d-flex flex-row justify-content-end">
                                      <div class="chart-body-heading-two">
                                        <span>Back</span>
                                      </div>
                                    </div>
                                    <div class="chart-body-heading chart-body-heading-outer-two chart-body-heading-outer d-flex flex-row">
                                      <div class="chart-body-heading-one">
                                        <span>Lay</span>
                                      </div>
                                    </div>
                                  </div>
                                </div>
                              </div>
                            </div>
                          </div>
                          <div v-if="bookmaker.msg != null && bookmaker.msg != '' && bookmaker.msg != ' '" class="chart-body-heading-block">
                            <div class="fancy-msg-block">
                              <div class="fancy-msg">
                                <marquee>{{ bookmaker.msg }}</marquee>
                              </div>
                            </div>
                          </div>
                          <div :class="bookmaker.is_sus ? 'chart-body suspended' : 'chart-body'" data-content="suspended">
                            <ul v-if="bookmaker.is_sus">
                              <li v-for="(bookmaker_runner_odd, bookmaker_runner_odd_index) in bookmaker.runner_odds">
                                <div class="match-odds-outer d-flex justify-content-between flex-row">
                                  <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                    <div class="match-odds-title-block">
                                      <h5 class="match-odds-title">
                                        {{ bookmaker_runner_odd.runner_name }}
                                      </h5>
                                      <div v-if="getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).is_exists" :class="getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).runner_book > 0 ? 'match-odds-tag danger' : getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).runner_book < 0 ? 'match-odds-tag success' : 'match-odds-tag'">
                                        <span>{{ getBookmakerBooks(bookmaker.id, bookmaker_runner_odd.selection_id).runner_book | positive | addCommas }}</span>
                                      </div>
                                    </div>
                                  </a>
                                  <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                    <h5 class="price-odd-title"></h5>
                                    <div class="price-odd"></div>
                                  </a>
                                  <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                    <h5 class="price-odd-title"></h5>
                                    <div class="price-odd"></div>
                                  </a>
                                  <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                    <h5 class="price-odd-title"></h5>
                                    <div class="price-odd"></div>
                                  </a>
                                  <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                    <h5 class="price-odd-title"></h5>
                                    <div class="price-odd"></div>
                                  </a>
                                  <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                    <h5 class="price-odd-title"></h5>
                                    <div class="price-odd"></div>
                                  </a>
                                  <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                    <h5 class="price-odd-title"></h5>
                                    <div class="price-odd"></div>
                                  </a>
                                </div>
                              </li>  
                              <li>
                                <div class="match-odds-outer d-flex justify-content-end flex-row min-max-text" style="margin-right: 3px; margin-top: 5px">
                                  Min:
                                  {{ currentSport.stake_size.min_bookmaker }} | Max:
                                  {{ currentSport.stake_size.max_bookmaker | kiloConverter }}
                                </div>
                              </li>
                            </ul>
                            <ul v-else>
                              <li v-for="(bookmaker_runner_odd, bookmaker_runner_odd_index) in bookmaker.runner_odds">
                                <div class="match-odds-outer d-flex justify-content-between flex-row">
                                  <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                    <div class="match-odds-title-block">
                                      <h5 class="match-odds-title">
                                        {{ bookmaker_runner_odd.runner_name }}
                                      </h5>
                                      <div v-if="getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).is_exists" :class="getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).runner_book > 0 ? 'match-odds-tag danger' : getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).runner_book < 0 ? 'match-odds-tag success' : 'match-odds-tag'">
                                        <span>{{ getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).runner_book | positive | addCommas }}</span>
                                      </div>
                                    </div>
                                  </a>
                                  <div v-if="bookmaker_runner_odd.is_sus" class="price-odds-items-block d-flex justify-content-between flex-row suspended suspended-small" data-content="suspended">
                                    <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                  </div>
                                  <div v-else class="price-odds-items-block d-flex justify-content-between flex-row">
                                    <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                      <h5 class="price-odd-title">
                                        {{ bookmaker_runner_odd.back }}
                                      </h5>
                                      <div class="price-odd" v-if="bookmaker_runner_odd.back == '' && bookmaker_runner_odd.back == null"></div>
                                      <div class="price-odd" v-else>
                                        {{ (bookmaker.stake_limit != null && bookmaker.stake_limit != "" ? bookmaker.stake_limit : currentSport.stake_size.max_bookmaker) | kiloConverter }}
                                      </div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                      <h5 class="price-odd-title">
                                        {{ bookmaker_runner_odd.lay }}
                                      </h5>
                                      <div class="price-odd" v-if="bookmaker_runner_odd.lay == '' && bookmaker_runner_odd.lay == null"></div>
                                      <div class="price-odd" v-else>
                                        {{ (bookmaker.stake_limit != null && bookmaker.stake_limit != "" ? bookmaker.stake_limit : currentSport.stake_size.max_bookmaker) | kiloConverter }}
                                      </div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                  </div>
                                </div>
                              </li>



                              <li v-if="currentSport.stake_size != null">
                                <div class="match-odds-outer d-flex justify-content-end flex-row min-max-text" style="margin-right: 3px; margin-top: 5px">
                                  Min:
                                  {{ currentSport.stake_size.min_bookmaker }}
                                  |&nbsp;
                                  <span v-if="bookmaker.stake_limit != null && bookmaker.stake_limit != ''">
                                    Max:
                                    {{ bookmaker.stake_limit | kiloConverter }}
                                  </span>
                                  <span v-else>
                                    Max:
                                    {{ currentSport.stake_size.max_bookmaker | kiloConverter }}
                                  </span>
                                </div>
                              </li>
                            </ul>
                          </div>
                        </div>
                        <div v-if="auth._role == 'is_director'">
                          <div class="chart-body-heading-block event-detail-manage-main-block">
                            <div class="event-detail-manage-block d-flex justify-content-between flex-row">
                              <h5>
                                {{ bookmaker.title }}
                                <span class="badge badge-primary">Bookmaker</span>
                              </h5>
                              <div class="chart-body-heading-details">
                                <div class="d-flex flex-row justify-content-between">
                                  <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                    <h6>SUSPEND</h6>
                                    <label :for="'suspend_bookmaker_' + bookmaker.id" class="button-toggle-wrap">
                                      <input :id="'suspend_bookmaker_' + bookmaker.id" class="toggler" type="checkbox" v-model="bookmaker.is_sus" @change="manageMarkets('Bookmaker', 'suspend', bookmaker)" />
                                      <div class="button-toggle danger">
                                        <div class="handle">
                                          <div class="bars"></div>
                                        </div>
                                      </div>
                                    </label>
                                  </div>
                                  <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                    <h6>ACTIVE</h6>
                                    <label :for="'active_bookmaker_' + bookmaker.id" class="button-toggle-wrap">
                                      <input :id="'active_bookmaker_' + bookmaker.id" class="toggler" type="checkbox" v-model="bookmaker.is_active" @change="manageMarkets('Bookmaker', 'active', bookmaker)" />
                                      <div class="button-toggle">
                                        <div class="handle">
                                          <div class="bars"></div>
                                        </div>
                                      </div>
                                    </label>
                                  </div>
                                  <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                    <label class="button-toggle-wrap">
                                      <div class="chart-body-heading-two" style="padding-right: 0px">
                                        <button href="javascript:void(0)" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default book-btn" title="Booking"  @click="openBookOrBetModal(null, 'B', bookmaker.title, '', 'book-bookmaker-market-modal', true, bookmaker.id, null)">Book</button>
                                      </div>
                                    </label>
                                  </div>

                                  <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                    <label class="button-toggle-wrap">
                                      <div class="chart-body-heading-two" style="padding-right: 0px">
                                        <button href="javascript:void(0)" style="background: #4f687d" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default bet-btn" title="Bets"  @click="openBookOrBetModal(null, 'BB', bookmaker.title, '', 'bets-fancy-bookmaker-market-modal', true, bookmaker.id)">Bets</button>
                                      </div>
                                    </label>
                                  </div>
                                </div>
                              </div>
                            </div>
                          </div>
                          <div class="chart-body-heading-block">
                            <div class="chart-body-heading">
                              <div class="d-flex justify-content-between flex-row" style="justify-content: flex-end !important">
                                <h6 class="chart-body-heading chart-body-main-heading" style="display: none">
                                  <span>{{ bookmaker.title }}</span>
                                </h6>
                                <div class="chart-body-heading-details">
                                  <div class="d-flex flex-row justify-content-between">
                                    <div class="chart-body-heading chart-body-heading-outer d-flex flex-row justify-content-end">
                                      <div class="chart-body-heading-two">
                                        <span>Back</span>
                                      </div>
                                    </div>
                                    <div class="chart-body-heading chart-body-heading-outer-two chart-body-heading-outer d-flex flex-row">
                                      <div class="chart-body-heading-one">
                                        <span>Lay</span>
                                      </div>
                                    </div>
                                  </div>
                                </div>
                              </div>
                            </div>
                          </div>
                          <div :class="bookmaker.is_sus ? 'chart-body suspended' : 'chart-body'" data-content="suspended">
                            <ul v-if="bookmaker.is_sus">
                              <li v-for="(bookmaker_runner_odd, bookmaker_runner_odd_index) in bookmaker.runner_odds">
                                <div class="match-odds-outer d-flex justify-content-between flex-row">
                                  <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                    <div class="match-odds-title-block">
                                      <h5 class="match-odds-title">
                                        {{ bookmaker_runner_odd.runner_name }}
                                      </h5>
                                      <div v-if="getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).is_exists" :class="getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).runner_book > 0 ? 'match-odds-tag danger' : getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).runner_book < 0 ? 'match-odds-tag success' : 'match-odds-tag'">
                                        <span>{{ getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).runner_book | positive | addCommas }}</span>
                                      </div>
                                    </div>
                                  </a>
                                  <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                    <h5 class="price-odd-title"></h5>
                                    <div class="price-odd"></div>
                                  </a>
                                  <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                    <h5 class="price-odd-title"></h5>
                                    <div class="price-odd"></div>
                                  </a>
                                  <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                    <h5 class="price-odd-title"></h5>
                                    <div class="price-odd"></div>
                                  </a>
                                  <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                    <h5 class="price-odd-title"></h5>
                                    <div class="price-odd"></div>
                                  </a>
                                  <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                    <h5 class="price-odd-title"></h5>
                                    <div class="price-odd"></div>
                                  </a>
                                  <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                    <h5 class="price-odd-title"></h5>
                                    <div class="price-odd"></div>
                                  </a>
                                </div>
                              </li>

                              <li>
                                <div class="match-odds-outer d-flex justify-content-end flex-row min-max-text" style="margin-right: 3px; margin-top: 5px">
                                  Min:
                                  {{ currentSport.stake_size.min_bookmaker }} | Max:
                                  {{ currentSport.stake_size.max_bookmaker | kiloConverter }}
                                </div>
                              </li>
                            </ul>
                            <ul v-else>
                              <li v-for="(bookmaker_runner_odd, bookmaker_runner_odd_index) in bookmaker.runner_odds">
                                <div class="match-odds-outer d-flex justify-content-between flex-row">
                                  <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                    <div class="match-odds-title-block">
                                      <h5 class="match-odds-title">
                                        {{ bookmaker_runner_odd.runner_name }}
                                      </h5>
                                      <div v-if="getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).is_exists" :class="getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).runner_book > 0 ? 'match-odds-tag danger' : getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).runner_book < 0 ? 'match-odds-tag success' : 'match-odds-tag'">
                                        <span>{{ getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).runner_book | positive | addCommas }}</span>
                                      </div>
                                    </div>
                                  </a>
                                  <div v-if="bookmaker_runner_odd.is_sus" class="price-odds-items-block d-flex justify-content-between flex-row suspended suspended-small" data-content="suspended">
                                    <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                  </div>
                                  <div v-else class="price-odds-items-block d-flex justify-content-between flex-row">
                                    <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                      <h5 class="price-odd-title">
                                        {{ bookmaker_runner_odd.back }}
                                      </h5>
                                      <div class="price-odd" v-if="bookmaker_runner_odd.back == '' && bookmaker_runner_odd.back == null"></div>
                                      <div class="price-odd" v-else>
                                        {{ (bookmaker.stake_limit != null && bookmaker.stake_limit != "" ? bookmaker.stake_limit : currentSport.stake_size.max_bookmaker) | kiloConverter }}
                                      </div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                      <h5 class="price-odd-title">
                                        {{ bookmaker_runner_odd.lay }}
                                      </h5>
                                      <div class="price-odd" v-if="bookmaker_runner_odd.lay == '' && bookmaker_runner_odd.lay == null"></div>
                                      <div class="price-odd" v-else>
                                        {{ (bookmaker.stake_limit != null && bookmaker.stake_limit != "" ? bookmaker.stake_limit : currentSport.stake_size.max_bookmaker) | kiloConverter }}
                                      </div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                  </div>
                                </div>
                              </li>
                              <li v-if="currentSport.stake_size != null">
                                <div class="match-odds-outer d-flex justify-content-end flex-row min-max-text" style="margin-right: 3px; margin-top: 5px">
                                  Min:
                                  {{ currentSport.stake_size.min_bookmaker }}
                                  |&nbsp;
                                  <span v-if="bookmaker.stake_limit != null && bookmaker.stake_limit != ''">
                                    Max:
                                    {{ bookmaker.stake_limit | kiloConverter }}
                                  </span>
                                  <span v-else>
                                    Max:
                                    {{ currentSport.stake_size.max_bookmaker | kiloConverter }}
                                  </span>
                                </div>
                              </li>
                            </ul>
                          </div>
                        </div>
                      </div>
                      <!-- End Bookmakers -->
                      <!-- All Fancies Odds -->
                      <div class="row desktop" style="width: 100%; margin: 0">
                        <div class="col-lg-6 pad-0" v-if="normal_fancies != null && (auth._role == 'is_director' || normal_fancies.is_running) && normal_fancies.data.length > 0">
                          <div class="chart-body-outer fancy-main-block border-1">
                            <div class="chart-body-heading-block">
                              <div class="chart-body-heading">
                                <div class="d-flex justify-content-between flex-row">
                                  <h6 class="chart-body-heading chart-body-main-heading">
                                    <span>{{ normal_fancies.name }}</span>
                                  </h6>
                                  <div class="chart-body-heading-details">
                                    <div class="d-flex flex-row justify-content-between">
                                      <div class="chart-body-heading chart-body-heading-outer d-flex flex-row justify-content-end">
                                        <div class="chart-body-heading-two">
                                          <span>No</span>
                                        </div>
                                      </div>
                                      <div class="chart-body-heading chart-body-heading-outer-two chart-body-heading-outer d-flex flex-row">
                                        <div class="chart-body-heading-one">
                                          <span>Yes</span>
                                        </div>
                                      </div>
                                    </div>
                                  </div>
                                </div>
                              </div>
                            </div>
                            <div class="chart-body">
                              <ul>
                                <li v-for="(fancy, normal_fancies_index) in normal_fancies.data" :key="normal_fancies_index">
                                  <div v-if="auth._role != 'is_director'">
                                    <div class="chart-body-heading-block event-detail-manage-main-block mb-1">
                                      <div class="event-detail-manage-block d-flex justify-content-end flex-row">
                                        <div class="chart-body-heading-details">
                                          <div class="d-flex flex-row justify-content-end">
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <label class="button-toggle-wrap float-right">
                                                <div class="chart-body-heading-two" style="padding-right: 0px">
                                                  <button href="javascript:void(0)" style="background: #4f687d" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default bet-btn" title="Bets"  @click="openBookOrBetModal(null, 'BF', fancy.runner_name, '', 'bets-fancy-bookmaker-market-modal', true, null, fancy.id)">Bets</button>
                                                </div>
                                              </label>
                                            </div>
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="match-odds-outer d-flex justify-content-between flex-row">
                                      <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                        <div class="match-odds-title-block" @click.prevent="showFancyBook(fancy.id)">
                                          <span :data-tooltip="fancy.is_com ? 'Commission' : 'No Commission'" data-tooltip-location="right" :class="fancy.is_com ? 'badge border-success badge-pill info badge-border fancy-com-text' : 'badge border-warning badge-pill warning badge-border fancy-com-text'">{{
                                            fancy.is_com ? "CM" : "NC"
                                          }}</span>
                                          <h5 class="match-odds-title">
                                            {{ fancy.runner_name }}
                                          </h5>
                                          <div class="match-odds-tag danger">
                                            <span>{{ fancy.exposure}}</span>
                                          </div>
                                        </div>
                                      </a>
                                      <div class="price-odds-items-block d-flex justify-content-between flex-row">
                                        <div v-if="fancy.is_sus" class="d-flex justify-content-between flex-row mr-8 suspended suspended-small pos-rel" data-content="suspended">
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                        </div>
                                        <div v-else class="d-flex justify-content-between flex-row mr-8 pos-rel">
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.no_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ fancy.no_odd != "" && fancy.no_odd != null ? fancy.no_bhav : "" }}
                                            </div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.yes_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ fancy.yes_odd != "" && fancy.yes_odd != null ? fancy.yes_bhav : "" }}
                                            </div>
                                          </a>
                                        </div>
                                        <div class="price-odds-min-max-block text-right min-max-text">
                                          <div class="price-odds-min-max-title">
                                            Min:
                                            {{ fancy.min_stake != null && fancy.min_stake != "" ? fancy.min_stake : currentSport.stake_size.min_fancy }}
                                          </div>
                                          <div class="price-odds-min-max-title">
                                            Max:
                                            {{ (fancy.max_stake != null && fancy.max_stake != "" ? fancy.max_stake : currentSport.stake_size.max_fancy) | kiloConverter }}
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="fancy-msg-block" v-if="fancy.msg != null && fancy.msg != ''">
                                      <div class="fancy-msg">
                                        <marquee>{{ fancy.msg }}</marquee>
                                      </div>
                                    </div>
                                  </div>
                                  <div v-else>
                                    <div class="chart-body-heading-block event-detail-manage-main-block">
                                      <div class="event-detail-manage-block d-flex justify-content-between flex-row">
                                        <h5>MANAGE</h5>
                                        <div class="chart-body-heading-details">
                                          <div class="d-flex flex-row justify-content-between">
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <h6>SUSPEND</h6>
                                              <label :for="'suspend_fancy_' + fancy.id" class="button-toggle-wrap">
                                                <input :id="'suspend_fancy_' + fancy.id" class="toggler" type="checkbox" v-model="fancy.is_sus" @change="manageMarkets('Fancy', 'suspend', fancy)" />
                                                <div class="button-toggle danger">
                                                  <div class="handle">
                                                    <div class="bars"></div>
                                                  </div>
                                                </div>
                                              </label>
                                            </div>
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <h6>ACTIVE</h6>
                                              <label :for="'active_fancy_' + fancy.id" class="button-toggle-wrap">
                                                <input :id="'active_fancy_' + fancy.id" class="toggler" type="checkbox" v-model="fancy.is_active" @change="manageMarkets('Fancy', 'active', fancy)" />
                                                <div class="button-toggle">
                                                  <div class="handle">
                                                    <div class="bars"></div>
                                                  </div>
                                                </div>
                                              </label>
                                            </div>
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <label class="button-toggle-wrap">
                                                <div class="chart-body-heading-two" style="padding-right: 0px">
                                                  <button href="javascript:void(0)" style="background: #4f687d" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default bet-btn" title="Bets"  @click="openBookOrBetModal(null, 'BF', fancy.runner_name, '', 'bets-fancy-bookmaker-market-modal', true, null, fancy.id)">Bets</button>
                                                </div>
                                              </label>
                                            </div>
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="match-odds-outer d-flex justify-content-between flex-row">
                                      <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                        <div class="match-odds-title-block" @click.prevent="showFancyBook(fancy.id)">
                                          <h5 class="match-odds-title">
                                            <span :data-tooltip="fancy.is_com ? 'Commission' : 'No Commission'" data-tooltip-location="right" :class="fancy.is_com ? 'badge border-success badge-pill info badge-border fancy-com-text' : 'badge border-warning badge-pill warning badge-border fancy-com-text'">{{
                                              fancy.is_com ? "CM" : "NC"
                                            }}</span>
                                            {{ fancy.runner_name }}
                                          </h5>
                                          <div class="match-odds-tag danger">
                                            <span>{{ fancy.exposure}}</span>
                                          </div>
                                        </div>
                                      </a>
                                      <div class="price-odds-items-block d-flex justify-content-between flex-row">
                                        <div v-if="fancy.is_sus" class="d-flex justify-content-between flex-row mr-8 suspended suspended-small pos-rel" data-content="suspended">
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                        </div>
                                        <div v-else class="d-flex justify-content-between flex-row mr-8 pos-rel">
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.no_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ fancy.no_odd != "" && fancy.no_odd != null ? fancy.no_bhav : "" }}
                                            </div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.yes_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ fancy.yes_odd != "" && fancy.yes_odd != null ? fancy.yes_bhav : "" }}
                                            </div>
                                          </a>
                                        </div>
                                        <div class="price-odds-min-max-block text-right min-max-text">
                                          <div class="price-odds-min-max-title">
                                            Min:
                                            {{ fancy.min_stake != null && fancy.min_stake != "" ? fancy.min_stake : currentSport.stake_size.min_fancy }}
                                          </div>
                                          <div class="price-odds-min-max-title">
                                            Max:
                                            {{ (fancy.max_stake != null && fancy.max_stake != "" ? fancy.max_stake : currentSport.stake_size.max_fancy) | kiloConverter }}
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="fancy-msg-block" v-if="fancy.msg != null && fancy.msg != ''">
                                      <div class="fancy-msg">
                                        <marquee>{{ fancy.msg }}</marquee>
                                      </div>
                                    </div>
                                  </div>
                                </li>
                              </ul>
                            </div>
                          </div>
                        </div>
                        <div class="col-lg-6 pad-0">
                          <div class="chart-body-outer fancy-main-block border-1" v-if="ball_by_ball_fancies != null && (auth._role == 'is_director' || ball_by_ball_fancies.is_running) && ball_by_ball_fancies.data.length > 0">
                            <div class="chart-body-heading-block">
                              <div class="chart-body-heading">
                                <div class="d-flex justify-content-between flex-row">
                                  <h6 class="chart-body-heading chart-body-main-heading">
                                    <span>{{ ball_by_ball_fancies.name }}</span>
                                  </h6>
                                  <div class="chart-body-heading-details">
                                    <div class="d-flex flex-row justify-content-between">
                                      <div class="chart-body-heading chart-body-heading-outer d-flex flex-row justify-content-end">
                                        <div class="chart-body-heading-two">
                                          <span>No</span>
                                        </div>
                                      </div>
                                      <div class="chart-body-heading chart-body-heading-outer-two chart-body-heading-outer d-flex flex-row">
                                        <div class="chart-body-heading-one">
                                          <span>Yes</span>
                                        </div>
                                      </div>
                                    </div>
                                  </div>
                                </div>
                              </div>
                            </div>
                            <div class="chart-body">
                              <ul>
                                <li v-for="(fancy, ball_by_ball_fancies_index) in ball_by_ball_fancies.data" :key="ball_by_ball_fancies_index">
                                  <div v-if="auth._role != 'is_director'">
                                    <div class="chart-body-heading-block event-detail-manage-main-block mb-1">
                                      <div class="event-detail-manage-block d-flex justify-content-end flex-row">
                                        <div class="chart-body-heading-details">
                                          <div class="d-flex flex-row justify-content-end">
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <label class="button-toggle-wrap float-right">
                                                <div class="chart-body-heading-two" style="padding-right: 0px">
                                                  <button href="javascript:void(0)" style="background: #4f687d" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default bet-btn" title="Bets"  @click="openBookOrBetModal(null, 'BF', fancy.runner_name, '', 'bets-fancy-bookmaker-market-modal', true, null, fancy.id)">Bets</button>
                                                </div>
                                              </label>
                                            </div>
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="match-odds-outer d-flex justify-content-between flex-row">
                                      <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                        <div class="match-odds-title-block" @click.prevent="showFancyBook(fancy.id)">
                                          <span :data-tooltip="fancy.is_com ? 'Commission' : 'No Commission'" data-tooltip-location="right" :class="fancy.is_com ? 'badge border-success badge-pill info badge-border fancy-com-text' : 'badge border-warning badge-pill warning badge-border fancy-com-text'">{{
                                            fancy.is_com ? "CM" : "NC"
                                          }}</span>
                                          <h5 class="match-odds-title">
                                            {{ fancy.runner_name }}
                                          </h5>
                                          <div class="match-odds-tag danger">
                                            <span>{{ fancy.exposure}}</span>
                                          </div>
                                        </div>
                                      </a>
                                      <div class="price-odds-items-block d-flex justify-content-between flex-row">
                                        <div v-if="fancy.is_sus" class="d-flex justify-content-between flex-row mr-8 suspended suspended-small pos-rel" data-content="suspended">
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                        </div>
                                        <div v-else class="d-flex justify-content-between flex-row mr-8 pos-rel">
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.no_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ fancy.no_odd != "" && fancy.no_odd != null ? fancy.no_bhav : "" }}
                                            </div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.yes_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ fancy.yes_odd != "" && fancy.yes_odd != null ? fancy.yes_bhav : "" }}
                                            </div>
                                          </a>
                                        </div>
                                        <div class="price-odds-min-max-block text-right min-max-text">
                                          <div class="price-odds-min-max-title">
                                            Min:
                                            {{ fancy.min_stake != null && fancy.min_stake != "" ? fancy.min_stake : currentSport.stake_size.min_fancy }}
                                          </div>
                                          <div class="price-odds-min-max-title">
                                            Max:
                                            {{ (fancy.max_stake != null && fancy.max_stake != "" ? fancy.max_stake : currentSport.stake_size.max_fancy) | kiloConverter }}
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="fancy-msg-block" v-if="fancy.msg != null && fancy.msg != ''">
                                      <div class="fancy-msg">
                                        <marquee>{{ fancy.msg }}</marquee>
                                      </div>
                                    </div>
                                  </div>
                                  <div v-else>
                                    <div class="chart-body-heading-block event-detail-manage-main-block">
                                      <div class="event-detail-manage-block d-flex justify-content-between flex-row">
                                        <h5>MANAGE</h5>
                                        <div class="chart-body-heading-details">
                                          <div class="d-flex flex-row justify-content-between">
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <h6>SUSPEND</h6>
                                              <label :for="'suspend_fancy_' + fancy.id" class="button-toggle-wrap">
                                                <input :id="'suspend_fancy_' + fancy.id" class="toggler" type="checkbox" v-model="fancy.is_sus" @change="manageMarkets('Fancy', 'suspend', fancy)" />
                                                <div class="button-toggle danger">
                                                  <div class="handle">
                                                    <div class="bars"></div>
                                                  </div>
                                                </div>
                                              </label>
                                            </div>
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <h6>ACTIVE</h6>
                                              <label :for="'active_fancy_' + fancy.id" class="button-toggle-wrap">
                                                <input :id="'active_fancy_' + fancy.id" class="toggler" type="checkbox" v-model="fancy.is_active" @change="manageMarkets('Fancy', 'active', fancy)" />
                                                <div class="button-toggle">
                                                  <div class="handle">
                                                    <div class="bars"></div>
                                                  </div>
                                                </div>
                                              </label>
                                            </div>

                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <label class="button-toggle-wrap">
                                                <div class="chart-body-heading-two" style="padding-right: 0px">
                                                  <button href="javascript:void(0)" style="background: #4f687d" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default bet-btn" title="Bets"  @click="openBookOrBetModal(null, 'BF', fancy.runner_name, '', 'bets-fancy-bookmaker-market-modal', true, null, fancy.id)">Bets</button>
                                                </div>
                                              </label>
                                            </div>
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="match-odds-outer d-flex justify-content-between flex-row">
                                      <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                        <div class="match-odds-title-block" @click.prevent="showFancyBook(fancy.id)">
                                          <span :data-tooltip="fancy.is_com ? 'Commission' : 'No Commission'" data-tooltip-location="right" :class="fancy.is_com ? 'badge border-success badge-pill info badge-border fancy-com-text' : 'badge border-warning badge-pill warning badge-border fancy-com-text'">{{
                                            fancy.is_com ? "CM" : "NC"
                                          }}</span>
                                          <h5 class="match-odds-title">
                                            {{ fancy.runner_name }}
                                          </h5>
                                          <div class="match-odds-tag danger">
                                            <span>{{ fancy.exposure}}</span>
                                          </div>
                                        </div>
                                      </a>
                                      <div class="price-odds-items-block d-flex justify-content-between flex-row">
                                        <div v-if="fancy.is_sus" class="d-flex justify-content-between flex-row mr-8 suspended suspended-small pos-rel" data-content="suspended">
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                        </div>
                                        <div v-else class="d-flex justify-content-between flex-row mr-8 pos-rel">
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.no_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ fancy.no_odd != "" && fancy.no_odd != null ? fancy.no_bhav : "" }}
                                            </div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.yes_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ fancy.yes_odd != "" && fancy.yes_odd != null ? fancy.yes_bhav : "" }}
                                            </div>
                                          </a>
                                        </div>
                                        <div class="price-odds-min-max-block text-right min-max-text">
                                          <div class="price-odds-min-max-title">
                                            Min:
                                            {{ fancy.min_stake != null && fancy.min_stake != "" ? fancy.min_stake : currentSport.stake_size.min_fancy }}
                                          </div>
                                          <div class="price-odds-min-max-title">
                                            Max:
                                            {{ (fancy.max_stake != null && fancy.max_stake != "" ? fancy.max_stake : currentSport.stake_size.max_fancy) | kiloConverter }}
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="fancy-msg-block" v-if="fancy.msg != null && fancy.msg != ''">
                                      <div class="fancy-msg">
                                        <marquee>{{ fancy.msg }}</marquee>
                                      </div>
                                    </div>
                                  </div>
                                </li>
                              </ul>
                            </div>
                          </div>
                          <div class="chart-body-outer fancy-main-block border-1" v-if="khado_fancies != null && (auth._role == 'is_director' || khado_fancies.is_running) && khado_fancies.data.length > 0">
                            <div class="chart-body-heading-block">
                              <div class="chart-body-heading">
                                <div class="d-flex justify-content-between flex-row">
                                  <h6 class="chart-body-heading chart-body-main-heading">
                                    <span>{{ khado_fancies.name }}</span>
                                  </h6>
                                  <div class="chart-body-heading-details">
                                    <div class="d-flex flex-row justify-content-between">
                                      <div class="chart-body-heading chart-body-heading-outer d-flex flex-row justify-content-end">
                                        <div class="chart-body-heading-two">
                                          <span>Back</span>
                                        </div>
                                      </div>
                                      <div class="chart-body-heading chart-body-heading-outer-two chart-body-heading-outer d-flex flex-row">
                                        <div class="chart-body-heading-one">
                                          <span>Lay</span>
                                        </div>
                                      </div>
                                    </div>
                                  </div>
                                </div>
                              </div>
                            </div>
                            <div class="chart-body">
                              <ul>
                                <li v-for="(fancy, khado_fancies_index) in khado_fancies.data" :key="khado_fancies_index">
                                  <div v-if="auth._role != 'is_director'">
                                    <div class="chart-body-heading-block event-detail-manage-main-block mb-1">
                                      <div class="event-detail-manage-block d-flex justify-content-end flex-row">
                                        <div class="chart-body-heading-details">
                                          <div class="d-flex flex-row justify-content-end">
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <label class="button-toggle-wrap float-right">
                                                <div class="chart-body-heading-two" style="padding-right: 0px">
                                                  <button href="javascript:void(0)" style="background: #4f687d" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default bet-btn" title="Bets"  @click="openBookOrBetModal(null, 'BF', fancy.runner_name, '', 'bets-fancy-bookmaker-market-modal', true, null, fancy.id)">Bets</button>
                                                </div>
                                              </label>
                                            </div>
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="match-odds-outer d-flex justify-content-between flex-row">
                                      <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                        <div class="match-odds-title-block" @click.prevent="showFancyBook(fancy.id)">
                                          <span :data-tooltip="fancy.is_com ? 'Commission' : 'No Commission'" data-tooltip-location="right" :class="fancy.is_com ? 'badge border-success badge-pill info badge-border fancy-com-text' : 'badge border-warning badge-pill warning badge-border fancy-com-text'">{{
                                            fancy.is_com ? "CM" : "NC"
                                          }}</span>
                                          <h5 class="match-odds-title">
                                            {{ fancy.runner_name }}
                                          </h5>
                                          <div class="match-odds-tag danger">
                                            <span>{{ fancy.exposure}}</span>
                                          </div>
                                        </div>
                                      </a>
                                      <div class="price-odds-items-block d-flex justify-content-between flex-row">
                                        <div v-if="fancy.is_sus" class="d-flex justify-content-between flex-row mr-8 suspended suspended-small pos-rel" data-content="suspended">
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                        </div>
                                        <div v-else class="d-flex justify-content-between flex-row mr-8 pos-rel">
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price" ss="price-odd-title"
                                            ><h5>{{ fancy.yes_odd }}</h5>
                                            <div class="price-odd">
                                              {{ fancy.yes_odd != "" && fancy.yes_odd != null ? fancy.yes_bhav : "" }}
                                            </div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                        </div>
                                        <div class="price-odds-min-max-block text-right min-max-text">
                                          <div class="price-odds-min-max-title">
                                            Min:
                                            {{ fancy.min_stake != null && fancy.min_stake != "" ? fancy.min_stake : currentSport.stake_size.min_fancy }}
                                          </div>
                                          <div class="price-odds-min-max-title">
                                            Max:
                                            {{ (fancy.max_stake != null && fancy.max_stake != "" ? fancy.max_stake : currentSport.stake_size.max_fancy) | kiloConverter }}
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="fancy-msg-block" v-if="fancy.msg != null && fancy.msg != ''">
                                      <div class="fancy-msg">
                                        <marquee>{{ fancy.msg }}</marquee>
                                      </div>
                                    </div>
                                  </div>
                                  <div v-else>
                                    <div class="chart-body-heading-block event-detail-manage-main-block">
                                      <div class="event-detail-manage-block d-flex justify-content-between flex-row">
                                        <h5>MANAGE</h5>
                                        <div class="chart-body-heading-details">
                                          <div class="d-flex flex-row justify-content-between">
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <h6>SUSPEND</h6>
                                              <label :for="'suspend_fancy_' + fancy.id" class="button-toggle-wrap">
                                                <input :id="'suspend_fancy_' + fancy.id" class="toggler" type="checkbox" v-model="fancy.is_sus" @change="manageMarkets('Fancy', 'suspend', fancy)" />
                                                <div class="button-toggle danger">
                                                  <div class="handle">
                                                    <div class="bars"></div>
                                                  </div>
                                                </div>
                                              </label>
                                            </div>
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <h6>ACTIVE</h6>
                                              <label :for="'active_fancy_' + fancy.id" class="button-toggle-wrap">
                                                <input :id="'active_fancy_' + fancy.id" class="toggler" type="checkbox" v-model="fancy.is_active" @change="manageMarkets('Fancy', 'active', fancy)" />
                                                <div class="button-toggle">
                                                  <div class="handle">
                                                    <div class="bars"></div>
                                                  </div>
                                                </div>
                                              </label>
                                            </div>
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <label class="button-toggle-wrap">
                                                <div class="chart-body-heading-two" style="padding-right: 0px">
                                                  <button href="javascript:void(0)" style="background: #4f687d" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default bet-btn" title="Bets"  @click="openBookOrBetModal(null, 'BF', fancy.runner_name, '', 'bets-fancy-bookmaker-market-modal', true, null, fancy.id)">Bets</button>
                                                </div>
                                              </label>
                                            </div>
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="match-odds-outer d-flex justify-content-between flex-row">
                                      <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                        <div class="match-odds-title-block" @click.prevent="showFancyBook(fancy.id)">
                                          <span :data-tooltip="fancy.is_com ? 'Commission' : 'No Commission'" data-tooltip-location="right" :class="fancy.is_com ? 'badge border-success badge-pill info badge-border fancy-com-text' : 'badge border-warning badge-pill warning badge-border fancy-com-text'">{{
                                            fancy.is_com ? "CM" : "NC"
                                          }}</span>
                                          <h5 class="match-odds-title">
                                            {{ fancy.runner_name }}
                                          </h5>
                                          <div class="match-odds-tag danger">
                                            <span>{{ fancy.exposure}}</span>
                                          </div>
                                        </div>
                                      </a>
                                      <div class="price-odds-items-block d-flex justify-content-between flex-row">
                                        <div v-if="fancy.is_sus" class="d-flex justify-content-between flex-row mr-8 suspended suspended-small pos-rel" data-content="suspended">
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                        </div>
                                        <div v-else class="d-flex justify-content-between flex-row mr-8 pos-rel">
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.yes_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ fancy.yes_odd != "" && fancy.yes_odd != null ? fancy.yes_bhav : "" }}
                                            </div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                        </div>
                                        <div class="price-odds-min-max-block text-right min-max-text">
                                          <div class="price-odds-min-max-title">
                                            Min:
                                            {{ fancy.min_stake != null && fancy.min_stake != "" ? fancy.min_stake : currentSport.stake_size.min_fancy }}
                                          </div>
                                          <div class="price-odds-min-max-title">
                                            Max:
                                            {{ (fancy.max_stake != null && fancy.max_stake != "" ? fancy.max_stake : currentSport.stake_size.max_fancy) | kiloConverter }}
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="fancy-msg-block" v-if="fancy.msg != null && fancy.msg != ''">
                                      <div class="fancy-msg">
                                        <marquee>{{ fancy.msg }}</marquee>
                                      </div>
                                    </div>
                                  </div>
                                </li>
                              </ul>
                            </div>
                          </div>
                          <div class="chart-body-outer fancy-main-block border-1" v-if="meter_fancies != null && (auth._role == 'is_director' || meter_fancies.is_running) && meter_fancies.data.length > 0">
                            <div class="chart-body-heading-block">
                              <div class="chart-body-heading">
                                <div class="d-flex justify-content-between flex-row">
                                  <h6 class="chart-body-heading chart-body-main-heading">
                                    <span>{{ meter_fancies.name }}</span>
                                  </h6>
                                  <div class="chart-body-heading-details">
                                    <div class="d-flex flex-row justify-content-between">
                                      <div class="chart-body-heading chart-body-heading-outer d-flex flex-row justify-content-end">
                                        <div class="chart-body-heading-two">
                                          <span>No</span>
                                        </div>
                                      </div>
                                      <div class="chart-body-heading chart-body-heading-outer-two chart-body-heading-outer d-flex flex-row">
                                        <div class="chart-body-heading-one">
                                          <span>Yes</span>
                                        </div>
                                      </div>
                                    </div>
                                  </div>
                                </div>
                              </div>
                            </div>
                            <div class="chart-body">
                              <ul>
                                <li v-for="(fancy, meter_fancies_index) in meter_fancies.data" :key="meter_fancies_index">
                                  <div v-if="auth._role != 'is_director'">
                                    <div class="chart-body-heading-block event-detail-manage-main-block mb-1">
                                      <div class="event-detail-manage-block d-flex justify-content-end flex-row">
                                        <div class="chart-body-heading-details">
                                          <div class="d-flex flex-row justify-content-end">
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <label class="button-toggle-wrap float-right">
                                                <div class="chart-body-heading-two" style="padding-right: 0px">
                                                  <button href="javascript:void(0)" style="background: #4f687d" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default bet-btn" title="Bets"  @click="openBookOrBetModal(null, 'BF', fancy.runner_name, '', 'bets-fancy-bookmaker-market-modal', true, null, fancy.id)">Bets</button>
                                                </div>
                                              </label>
                                            </div>
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="match-odds-outer d-flex justify-content-between flex-row">
                                      <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                        <div class="match-odds-title-block" @click.prevent="showFancyBook(fancy.id)">
                                          <span :data-tooltip="fancy.is_com ? 'Commission' : 'No Commission'" data-tooltip-location="right" :class="fancy.is_com ? 'badge border-success badge-pill info badge-border fancy-com-text' : 'badge border-warning badge-pill warning badge-border fancy-com-text'">{{
                                            fancy.is_com ? "CM" : "NC"
                                          }}</span>
                                          <h5 class="match-odds-title">
                                            {{ fancy.runner_name }}
                                          </h5>
                                          <div class="match-odds-tag danger">
                                            <span>{{ fancy.exposure}}</span>
                                          </div>
                                        </div>
                                      </a>
                                      <div class="price-odds-items-block d-flex justify-content-between flex-row">
                                        <div v-if="fancy.is_sus" class="d-flex justify-content-between flex-row mr-8 suspended suspended-small pos-rel" data-content="suspended">
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                        </div>
                                        <div v-else class="d-flex justify-content-between flex-row mr-8 pos-rel">
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.no_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ fancy.no_odd != "" && fancy.no_odd != null ? fancy.no_bhav : "" }}
                                            </div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.yes_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ fancy.yes_odd != "" && fancy.yes_odd != null ? fancy.yes_bhav : "" }}
                                            </div>
                                          </a>
                                        </div>
                                        <div class="price-odds-min-max-block text-right min-max-text">
                                          <div class="price-odds-min-max-title">
                                            Min:
                                            {{ fancy.min_stake != null && fancy.min_stake != "" ? fancy.min_stake : currentSport.stake_size.min_fancy }}
                                          </div>
                                          <div class="price-odds-min-max-title">
                                            Max:
                                            {{ (fancy.max_stake != null && fancy.max_stake != "" ? fancy.max_stake : currentSport.stake_size.max_fancy) | kiloConverter }}
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="fancy-msg-block" v-if="fancy.msg != null && fancy.msg != ''">
                                      <div class="fancy-msg">
                                        <marquee>{{ fancy.msg }}</marquee>
                                      </div>
                                    </div>
                                  </div>
                                  <div v-else>
                                    <div class="chart-body-heading-block event-detail-manage-main-block">
                                      <div class="event-detail-manage-block d-flex justify-content-between flex-row">
                                        <h5>MANAGE</h5>
                                        <div class="chart-body-heading-details">
                                          <div class="d-flex flex-row justify-content-between">
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <h6>SUSPEND</h6>
                                              <label :for="'suspend_fancy_' + fancy.id" class="button-toggle-wrap">
                                                <input :id="'suspend_fancy_' + fancy.id" class="toggler" type="checkbox" v-model="fancy.is_sus" @change="manageMarkets('Fancy', 'suspend', fancy)" />
                                                <div class="button-toggle danger">
                                                  <div class="handle">
                                                    <div class="bars"></div>
                                                  </div>
                                                </div>
                                              </label>
                                            </div>
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <h6>ACTIVE</h6>
                                              <label :for="'active_fancy_' + fancy.id" class="button-toggle-wrap">
                                                <input :id="'active_fancy_' + fancy.id" class="toggler" type="checkbox" v-model="fancy.is_active" @change="manageMarkets('Fancy', 'active', fancy)" />
                                                <div class="button-toggle">
                                                  <div class="handle">
                                                    <div class="bars"></div>
                                                  </div>
                                                </div>
                                              </label>
                                            </div>
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <label class="button-toggle-wrap">
                                                <div class="chart-body-heading-two" style="padding-right: 0px">
                                                  <button href="javascript:void(0)" style="background: #4f687d" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default bet-btn" title="Bets"  @click="openBookOrBetModal(null, 'BF', fancy.runner_name, '', 'bets-fancy-bookmaker-market-modal', true, null, fancy.id)">Bets</button>
                                                </div>
                                              </label>
                                            </div>
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="match-odds-outer d-flex justify-content-between flex-row">
                                      <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                        <div class="match-odds-title-block" @click.prevent="showFancyBook(fancy.id)">
                                          <span :data-tooltip="fancy.is_com ? 'Commission' : 'No Commission'" data-tooltip-location="right" :class="fancy.is_com ? 'badge border-success badge-pill info badge-border fancy-com-text' : 'badge border-warning badge-pill warning badge-border fancy-com-text'">{{
                                            fancy.is_com ? "CM" : "NC"
                                          }}</span>
                                          <h5 class="match-odds-title">
                                            {{ fancy.runner_name }}
                                          </h5>
                                          <div class="match-odds-tag danger">
                                            <span>{{ fancy.exposure}}</span>
                                          </div>
                                        </div>
                                      </a>
                                      <div class="price-odds-items-block d-flex justify-content-between flex-row">
                                        <div v-if="fancy.is_sus" class="d-flex justify-content-between flex-row mr-8 suspended suspended-small pos-rel" data-content="suspended">
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                        </div>
                                        <div v-else class="d-flex justify-content-between flex-row mr-8 pos-rel">
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.no_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ fancy.no_odd != "" && fancy.no_odd != null ? fancy.no_bhav : "" }}
                                            </div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.yes_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ fancy.yes_odd != "" && fancy.yes_odd != null ? fancy.yes_bhav : "" }}
                                            </div>
                                          </a>
                                        </div>
                                        <div class="price-odds-min-max-block text-right min-max-text">
                                          <div class="price-odds-min-max-title">
                                            Min:
                                            {{ fancy.min_stake != null && fancy.min_stake != "" ? fancy.min_stake : currentSport.stake_size.min_fancy }}
                                          </div>
                                          <div class="price-odds-min-max-title">
                                            Max:
                                            {{ (fancy.max_stake != null && fancy.max_stake != "" ? fancy.max_stake : currentSport.stake_size.max_fancy) | kiloConverter }}
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="fancy-msg-block" v-if="fancy.msg != null && fancy.msg != ''">
                                      <div class="fancy-msg">
                                        <marquee>{{ fancy.msg }}</marquee>
                                      </div>
                                    </div>
                                  </div>
                                </li>
                              </ul>
                            </div>
                          </div>
                          <div class="chart-body-outer fancy-main-block border-1" v-if="fancy_1_fancies != null && (auth._role == 'is_director' || fancy_1_fancies.is_running) && fancy_1_fancies.data.length > 0">
                            <div class="chart-body-heading-block">
                              <div class="chart-body-heading">
                                <div class="d-flex justify-content-between flex-row">
                                  <h6 class="chart-body-heading chart-body-main-heading">
                                    <span>{{ fancy_1_fancies.name }}</span>
                                  </h6>
                                  <div class="chart-body-heading-details">
                                    <div class="d-flex flex-row justify-content-between">
                                      <div class="chart-body-heading chart-body-heading-outer d-flex flex-row justify-content-end">
                                        <div class="chart-body-heading-two">
                                          <span>Back</span>
                                        </div>
                                      </div>
                                      <div class="chart-body-heading chart-body-heading-outer-two chart-body-heading-outer d-flex flex-row">
                                        <div class="chart-body-heading-one">
                                          <span>Lay</span>
                                        </div>
                                      </div>
                                    </div>
                                  </div>
                                </div>
                              </div>
                            </div>
                            <div class="chart-body">
                              <ul>
                                <li v-for="(fancy, fancy_1_fancies_index) in fancy_1_fancies.data" :key="fancy_1_fancies_index">
                                  <div v-if="auth._role != 'is_director'">
                                    <div class="chart-body-heading-block event-detail-manage-main-block mb-1">
                                      <div class="event-detail-manage-block d-flex justify-content-end flex-row">
                                        <div class="chart-body-heading-details">
                                          <div class="d-flex flex-row justify-content-end">
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <label class="button-toggle-wrap float-right">
                                                <div class="chart-body-heading-two" style="padding-right: 0px">
                                                  <button href="javascript:void(0)" style="background: #4f687d" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default bet-btn" title="Bets"  @click="openBookOrBetModal(null, 'BF', fancy.runner_name, '', 'bets-fancy-bookmaker-market-modal', true, null, fancy.id)">Bets</button>
                                                </div>
                                              </label>
                                            </div>
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="match-odds-outer d-flex justify-content-between flex-row">
                                      <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                        <div class="match-odds-title-block">
                                          <span :data-tooltip="fancy.is_com ? 'Commission' : 'No Commission'" data-tooltip-location="right" :class="fancy.is_com ? 'badge border-success badge-pill info badge-border fancy-com-text' : 'badge border-warning badge-pill warning badge-border fancy-com-text'">{{
                                            fancy.is_com ? "CM" : "NC"
                                          }}</span>
                                          <h5 class="match-odds-title">
                                            {{ fancy.runner_name }}
                                          </h5>
                                          <div class="match-odds-tag danger">
                                            <span>{{ fancy.exposure}}</span>
                                          </div>
                                        </div>
                                      </a>
                                      <div class="price-odds-items-block d-flex justify-content-between flex-row">
                                        <div v-if="fancy.is_sus" class="d-flex justify-content-between flex-row mr-8 suspended suspended-small pos-rel" data-content="suspended">
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                        </div>
                                        <div v-else class="d-flex justify-content-between flex-row mr-8 pos-rel">
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.yes_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ (fancy.yes_odd != "" && fancy.yes_odd != null ? (fancy.max_stake != null && fancy.max_stake != "" ? fancy.max_stake : currentSport.stake_size.max_fancy) : "") | kiloConverter }}
                                            </div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.no_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ (fancy.no_odd != "" && fancy.no_odd != null ? (fancy.max_stake != null && fancy.max_stake != "" ? fancy.max_stake : currentSport.stake_size.max_fancy) : "") | kiloConverter }}
                                            </div>
                                          </a>
                                        </div>
                                        <div class="price-odds-min-max-block text-right min-max-text">
                                          <div class="price-odds-min-max-title">
                                            Min:
                                            {{ fancy.min_stake != null && fancy.min_stake != "" ? fancy.min_stake : currentSport.stake_size.min_fancy }}
                                          </div>
                                          <div class="price-odds-min-max-title">
                                            Max:
                                            {{ (fancy.max_stake != null && fancy.max_stake != "" ? fancy.max_stake : currentSport.stake_size.max_fancy) | kiloConverter }}
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="fancy-msg-block" v-if="fancy.msg != null && fancy.msg != ''">
                                      <div class="fancy-msg">
                                        <marquee>{{ fancy.msg }}</marquee>
                                      </div>
                                    </div>
                                  </div>
                                  <div v-else>
                                    <div class="chart-body-heading-block event-detail-manage-main-block">
                                      <div class="event-detail-manage-block d-flex justify-content-between flex-row">
                                        <h5>MANAGE</h5>
                                        <div class="chart-body-heading-details">
                                          <div class="d-flex flex-row justify-content-between">
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <h6>SUSPEND</h6>
                                              <label :for="'suspend_fancy_' + fancy.id" class="button-toggle-wrap">
                                                <input :id="'suspend_fancy_' + fancy.id" class="toggler" type="checkbox" v-model="fancy.is_sus" @change="manageMarkets('Fancy', 'suspend', fancy)" />
                                                <div class="button-toggle danger">
                                                  <div class="handle">
                                                    <div class="bars"></div>
                                                  </div>
                                                </div>
                                              </label>
                                            </div>
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <h6>ACTIVE</h6>
                                              <label :for="'active_fancy_' + fancy.id" class="button-toggle-wrap">
                                                <input :id="'active_fancy_' + fancy.id" class="toggler" type="checkbox" v-model="fancy.is_active" @change="manageMarkets('Fancy', 'active', fancy)" />
                                                <div class="button-toggle">
                                                  <div class="handle">
                                                    <div class="bars"></div>
                                                  </div>
                                                </div>
                                              </label>
                                            </div>
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <label class="button-toggle-wrap">
                                                <div class="chart-body-heading-two" style="padding-right: 0px">
                                                  <button href="javascript:void(0)" style="background: #4f687d" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default bet-btn" title="Bets"  @click="openBookOrBetModal(null, 'BF', fancy.runner_name, '', 'bets-fancy-bookmaker-market-modal', true, null, fancy.id)">Bets</button>
                                                </div>
                                              </label>
                                            </div>
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="match-odds-outer d-flex justify-content-between flex-row">
                                      <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                        <div class="match-odds-title-block">
                                          <span :data-tooltip="fancy.is_com ? 'Commission' : 'No Commission'" data-tooltip-location="right" :class="fancy.is_com ? 'badge border-success badge-pill info badge-border fancy-com-text' : 'badge border-warning badge-pill warning badge-border fancy-com-text'">{{
                                            fancy.is_com ? "CM" : "NC"
                                          }}</span>
                                          <h5 class="match-odds-title">
                                            {{ fancy.runner_name }}
                                          </h5>
                                          <div class="match-odds-tag danger">
                                            <span>{{ fancy.exposure}}</span>
                                          </div>
                                        </div>
                                      </a>
                                      <div class="price-odds-items-block d-flex justify-content-between flex-row">
                                        <div v-if="fancy.is_sus" class="d-flex justify-content-between flex-row mr-8 suspended suspended-small pos-rel" data-content="suspended">
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                        </div>
                                        <div v-else class="d-flex justify-content-between flex-row mr-8 pos-rel">
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.yes_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ (fancy.yes_odd != "" && fancy.yes_odd != null ? (fancy.max_stake != null && fancy.max_stake != "" ? fancy.max_stake : currentSport.stake_size.max_fancy) : "") | kiloConverter }}
                                            </div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.no_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ (fancy.no_odd != "" && fancy.no_odd != null ? (fancy.max_stake != null && fancy.max_stake != "" ? fancy.max_stake : currentSport.stake_size.max_fancy) : "") | kiloConverter }}
                                            </div>
                                          </a>
                                        </div>
                                        <div class="price-odds-min-max-block text-right min-max-text">
                                          <div class="price-odds-min-max-title">
                                            Min:
                                            {{ fancy.min_stake != null && fancy.min_stake != "" ? fancy.min_stake : currentSport.stake_size.min_fancy }}
                                          </div>
                                          <div class="price-odds-min-max-title">
                                            Max:
                                            {{ (fancy.max_stake != null && fancy.max_stake != "" ? fancy.max_stake : currentSport.stake_size.max_fancy) | kiloConverter }}
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="fancy-msg-block" v-if="fancy.msg != null && fancy.msg != ''">
                                      <div class="fancy-msg">
                                        <marquee>{{ fancy.msg }}</marquee>
                                      </div>
                                    </div>
                                  </div>
                                </li>
                              </ul>
                            </div>
                          </div>
                        </div>
                      </div>
                      <!-- End Fancy  new developer -->
                      <div class="chart-body-outer" v-if="market.is_active && market.is_declared == 0 && market.market_name != 'Match Odds'" v-for="(market, mIndex) in markets">
                        <div class="chart-body-heading-block">
                          <div class="chart-body-heading">
                            <div class="d-flex justify-content-between flex-row">
                              <h6 class="chart-body-heading chart-body-main-heading pt-1">
                                <span>{{ market.market_name }}</span>
                              </h6>
                              <div class="chart-body-heading-details">
                                <div class="d-flexnpm run flex-row justify-content-between">
                                  <div class="chart-body-heading chart-body-heading-outer d-flex flex-row justify-content-end" style="line-height: 29px">
                                    <div class="chart-body-heading-two">
                                      <button href="javascript:void(0)" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default book-btn" title="Booking"  @click="openBookOrBetModal(market.id, 'M', market.market_name, '', 'book-bookmaker-market-modal', true)">Book</button>
                                    </div>
                                    <div class="chart-body-heading-two">
                                      <button href="javascript:void(0)" style="background: #4f687d" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default bet-btn" title="Bets"  @click="openBookOrBetModal(market.id, 'BM', market.market_name, '', 'bets-fancy-bookmaker-market-modal', true, null, null)">Bets</button>
                                    </div>
                                  </div>
                                </div>
                              </div>
                            </div>
                          </div>
                        </div>

                        <div class="chart-body-heading-block" style="margin-top: 10px">
                          <div class="chart-body-heading">
                            <div class="d-flex justify-content-between flex-row" style="justify-content: flex-end !important">
                              <h6 class="chart-body-heading chart-body-main-heading" style="display: none">
                                {{ market.market_name }}
                              </h6>
                              <div class="chart-body-heading-details">
                                <div class="d-flex flex-row justify-content-between">
                                  <div class="chart-body-heading chart-body-heading-outer d-flex flex-row justify-content-end">
                                    <div class="chart-body-heading-two">
                                      <span>Back</span>
                                    </div>
                                  </div>
                                  <div class="chart-body-heading chart-body-heading-outer-two chart-body-heading-outer d-flex flex-row">
                                    <div class="chart-body-heading-one">
                                      <span>Lay</span>
                                    </div>
                                  </div>
                                </div>
                              </div>
                            </div>
                          </div>
                        </div>
                        <div :class="event.is_sus || event.is_active == 0 || market.is_sus || market.data == null ? 'chart-body suspended' : 'chart-body'" data-content="suspended">
                          <ul v-if="event.is_sus || event.is_active == 0 || market.is_sus || market.data == null">
                            <li v-for="(runner, rIndex) in market.selection_runner">
                              <div class="match-odds-outer d-flex justify-content-between flex-row">
                                <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                  <div class="match-odds-title-block">
                                    <h5 class="match-odds-title">
                                      {{ runner.runner_name }}
                                    </h5>
                                    <div v-for="(m_runner_books, runner_bookIndex) in market.runner_books" v-if="m_runner_books.selection_id == runner.selection_id" :class="m_runner_books.pl > 0 ? 'match-odds-tag danger' : m_runner_books.pl < 0 ? 'match-odds-tag success' : 'match-odds-tag'">
                                      <span>{{ m_runner_books.pl | positive | addCommas }}</span>
                                    </div>
                                  </div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                  <h5 class="price-odd-title"></h5>
                                  <div class="price-odd"></div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                  <h5 class="price-odd-title"></h5>
                                  <div class="price-odd"></div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                  <h5 class="price-odd-title"></h5>
                                  <div class="price-odd"></div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                  <h5 class="price-odd-title"></h5>
                                  <div class="price-odd"></div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                  <h5 class="price-odd-title"></h5>
                                  <div class="price-odd"></div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                  <h5 class="price-odd-title"></h5>
                                  <div class="price-odd"></div>
                                </a>
                              </div>
                            </li>
                            <li v-if="currentSport.stake_size != null">
                              <div class="match-odds-outer d-flex justify-content-end flex-row min-max-text" style="margin-right: 3px; margin-top: 5px">
                                Min:
                                {{ currentSport.stake_size.min_match }} |&nbsp;
                                <span v-if="event.is_custom != 1">Max: {{ event.inplay ? (market.inplay_stake_limit != null ? market.inplay_stake_limit : (event.match_setting != null && event.match_setting.is_active && (event.match_setting.exch_stake_limit != null && event.match_setting.exch_stake_limit != '') ? event.match_setting.exch_stake_limit : (market.stake_limit != null && market.stake_limit != '' ? market.stake_limit :  currentSport.stake_size.max_match))) : (event.match_setting != null && event.match_setting.is_active && (event.match_setting.exch_stake_limit != null && event.match_setting.exch_stake_limit != '') ? event.match_setting.exch_stake_limit : (market.stake_limit != null && market.stake_limit != '' ? market.stake_limit :  currentSport.stake_size.max_match)) | kiloConverter }}</span>
                                <span v-if="event.is_custom == 1"> Max: {{ event.match_setting != null && event.match_setting.is_active && (event.match_setting.exch_stake_limit != null && event.match_setting.exch_stake_limit != '') ? event.match_setting.exch_stake_limit : (market.stake_limit != null && market.stake_limit != '' ? market.stake_limit :  currentSport.stake_size.max_match) | kiloConverter }}</span>
                              </div>
                            </li>
                          </ul>
                          <ul v-else>
                            <li v-for="(runner, rIndex) in market.selection_runner">
                              
                              <div class="match-odds-outer d-flex justify-content-between flex-row" v-for="(data_runner, data_runner_Index) in market.data.runners" v-if="data_runner.selectionId == runner.selection_id">
                                <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                  <div class="match-odds-title-block">
                                    <h5 class="match-odds-title">
                                      {{ runner.runner_name }}
                                    </h5>
                                    <div  v-for="(m_runner_books, runner_bookIndex) in market.runner_books" v-if="m_runner_books.selection_id == runner.selection_id" :class="m_runner_books.pl > 0 ? 'match-odds-tag danger' : m_runner_books.pl < 0 ? 'match-odds-tag success' : 'match-odds-tag'">
                                      <span>{{ m_runner_books.pl | positive | addCommas }}</span>
                                    </div>
                                  </div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                  <h5 class="price-odd-title">
                                    {{ isset(data_runner.ex.availableToBack[2]) ? data_runner.ex.availableToBack[2].price : "" }}
                                  </h5>
                                  <div class="price-odd">
                                    {{ isset(data_runner.ex.availableToBack[2]) ? data_runner.ex.availableToBack[2].size : "" }}
                                  </div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                  <h5 class="price-odd-title">
                                    {{ isset(data_runner.ex.availableToBack[1]) ? data_runner.ex.availableToBack[1].price : "" }}
                                  </h5>
                                  <div class="price-odd">
                                    {{ isset(data_runner.ex.availableToBack[1]) ? data_runner.ex.availableToBack[1].size : "" }}
                                  </div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                  <h5 class="price-odd-title">
                                    {{ isset(data_runner.ex.availableToBack[0]) ? data_runner.ex.availableToBack[0].price : "" }}
                                  </h5>
                                  <div class="price-odd">
                                    {{ isset(data_runner.ex.availableToBack[0]) ? data_runner.ex.availableToBack[0].size : "" }}
                                  </div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                  <h5 class="price-odd-title">
                                    {{ isset(data_runner.ex.availableToLay[0]) ? data_runner.ex.availableToLay[0].price : "" }}
                                  </h5>
                                  <div class="price-odd">
                                    {{ isset(data_runner.ex.availableToLay[0]) ? data_runner.ex.availableToLay[0].size : "" }}
                                  </div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                  <h5 class="price-odd-title">
                                    {{ isset(data_runner.ex.availableToLay[1]) ? data_runner.ex.availableToLay[1].price : "" }}
                                  </h5>
                                  <div class="price-odd">
                                    {{ isset(data_runner.ex.availableToLay[1]) ? data_runner.ex.availableToLay[1].size : "" }}
                                  </div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                  <h5 class="price-odd-title">
                                    {{ isset(data_runner.ex.availableToLay[2]) ? data_runner.ex.availableToLay[2].price : "" }}
                                  </h5>
                                  <div class="price-odd">
                                    {{ isset(data_runner.ex.availableToLay[2]) ? data_runner.ex.availableToLay[2].size : "" }}
                                  </div>
                                </a>
                              </div>

                            </li>
                            <li v-if="currentSport.stake_size != null">
                              <div class="match-odds-outer d-flex justify-content-end flex-row min-max-text" style="margin-right: 3px; margin-top: 5px">
                                Min:
                                {{ currentSport.stake_size.min_match }} |&nbsp;
                                <span v-if="event.is_custom != 1">Max: {{ event.inplay ? (market.inplay_stake_limit != null ? market.inplay_stake_limit : (event.match_setting != null && event.match_setting.is_active && (event.match_setting.exch_stake_limit != null && event.match_setting.exch_stake_limit != '') ? event.match_setting.exch_stake_limit : (market.stake_limit != null && market.stake_limit != '' ? market.stake_limit :  currentSport.stake_size.max_match))) : (event.match_setting != null && event.match_setting.is_active && (event.match_setting.exch_stake_limit != null && event.match_setting.exch_stake_limit != '') ? event.match_setting.exch_stake_limit : (market.stake_limit != null && market.stake_limit != '' ? market.stake_limit :  currentSport.stake_size.max_match)) | kiloConverter }}</span>
                                <span v-if="event.is_custom == 1"> Max: {{ event.match_setting != null && event.match_setting.is_active && (event.match_setting.exch_stake_limit != null && event.match_setting.exch_stake_limit != '') ? event.match_setting.exch_stake_limit : (market.stake_limit != null && market.stake_limit != '' ? market.stake_limit :  currentSport.stake_size.max_match) | kiloConverter }}</span>
                              </div>
                            </li>
                          </ul>
                        </div>
                      </div>
                    </div>
                    <div class="tab-pane fade" id="exchange" role="tabpanel" aria-labelledby="exchange-tab">
                      <!-- Match Odds -->
                      <div class="chart-body-outer" v-if="market.is_active && market.is_declared == 0 && market.market_name == 'Match Odds'" v-for="(market, idx) in markets">
                        <div class="chart-body-heading-block">
                          <div class="chart-body-heading">
                            <div class="d-flex justify-content-between flex-row">
                              <h6 class="chart-body-heading chart-body-main-heading pt-1">
                                <span>{{ market.market_name }}</span>
                              </h6>
                              <div class="chart-body-heading-details">
                                <div class="d-flex flex-row justify-content-between">
                                  <div class="chart-body-heading chart-body-heading-outer d-flex flex-row justify-content-end">
                                    <div class="chart-body-heading-two">
                                      <button href="javascript:void(0)" style="font-size: 11px" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default book-btn" title="Booking"  @click="openBookOrBetModal(market.id, 'M', market.market_name, '', 'book-bookmaker-market-modal', true)">Book</button>
                                    </div>
                                    <div class="chart-body-heading-two">
                                      <button href="javascript:void(0)" style="font-size: 11px; background: #4f687d" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default bet-btn" title="Bets"  @click="openBookOrBetModal(market.id, 'BM', market.market_name, '', 'bets-fancy-bookmaker-market-modal', true, null, null)">Bets</button>
                                    </div>
                                  </div>
                                </div>
                              </div>
                            </div>
                          </div>
                        </div>
                        <div class="chart-body-heading-block">
                          <div class="chart-body-heading">
                            <div class="d-flex justify-content-between flex-row">
                              <h6 class="chart-body-heading chart-body-main-heading"></h6>
                              <div class="chart-body-heading-details">
                                <div class="d-flex flex-row justify-content-between">
                                  <div class="chart-body-heading chart-body-heading-outer d-flex flex-row justify-content-end">
                                    <div class="chart-body-heading-two">
                                      <span>Back</span>
                                    </div>
                                  </div>
                                  <div class="chart-body-heading chart-body-heading-outer-two chart-body-heading-outer d-flex flex-row">
                                    <div class="chart-body-heading-one">
                                      <span>Lay</span>
                                    </div>
                                  </div>
                                </div>
                              </div>
                            </div>
                          </div>
                        </div>
                        <div :class="event.is_sus || event.is_active == 0 || market.is_sus || market.data == null ? 'chart-body suspended' : 'chart-body'" data-content="suspended">
                          <ul v-if="event.is_sus || event.is_active == 0 || market.is_sus || market.data == null">
                            <li v-for="(runner, rIndex) in market.selection_runner">
                              <div class="match-odds-outer d-flex justify-content-between flex-row">
                                <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                  <div class="match-odds-title-block">
                                    <h5 class="match-odds-title">
                                      {{ runner.runner_name }}
                                    </h5>
                                    <div v-for="(m_runner_books, runner_bookIndex) in market.runner_books" v-if="m_runner_books.selection_id == runner.selection_id" :class="m_runner_books.pl > 0 ? 'match-odds-tag danger' : m_runner_books.pl < 0 ? 'match-odds-tag success' : 'match-odds-tag'">
                                      <span>{{ m_runner_books.pl | positive | addCommas }}</span>
                                    </div>
                                  </div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                  <h5 class="price-odd-title"></h5>
                                  <div class="price-odd"></div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                  <h5 class="price-odd-title"></h5>
                                  <div class="price-odd"></div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                  <h5 class="price-odd-title"></h5>
                                  <div class="price-odd"></div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                  <h5 class="price-odd-title"></h5>
                                  <div class="price-odd"></div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                  <h5 class="price-odd-title"></h5>
                                  <div class="price-odd"></div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                  <h5 class="price-odd-title"></h5>
                                  <div class="price-odd"></div>
                                </a>
                              </div>
                            </li>
                            <li v-if="currentSport.stake_size != null">
                              <div class="match-odds-outer d-flex justify-content-end flex-row min-max-text" style="margin-right: 3px; margin-top: 5px">
                                Min:
                                {{ currentSport.stake_size.min_match }} |&nbsp;
                                <span v-if="event.is_custom != 1">Max: {{ event.inplay ? (market.inplay_stake_limit != null ? market.inplay_stake_limit : (event.match_setting != null && event.match_setting.is_active && (event.match_setting.exch_stake_limit != null && event.match_setting.exch_stake_limit != '') ? event.match_setting.exch_stake_limit : (market.stake_limit != null && market.stake_limit != '' ? market.stake_limit :  currentSport.stake_size.max_match))) : (event.match_setting != null && event.match_setting.is_active && (event.match_setting.exch_stake_limit != null && event.match_setting.exch_stake_limit != '') ? event.match_setting.exch_stake_limit : (market.stake_limit != null && market.stake_limit != '' ? market.stake_limit :  currentSport.stake_size.max_match)) | kiloConverter }}</span>
                                <span v-if="event.is_custom == 1"> Max: {{ event.match_setting != null && event.match_setting.is_active && (event.match_setting.exch_stake_limit != null && event.match_setting.exch_stake_limit != '') ? event.match_setting.exch_stake_limit : (market.stake_limit != null && market.stake_limit != '' ? market.stake_limit :  currentSport.stake_size.max_match) | kiloConverter }}</span>
                              </div>
                            </li>
                          </ul>
                          <ul v-else>
                            <li v-for="(runner, rIndex) in market.selection_runner">
                              
                              <div class="match-odds-outer d-flex justify-content-between flex-row" v-for="(data_runner, data_runner_Index) in market.data.runners" v-if="data_runner.selectionId == runner.selection_id">
                                <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                  <div class="match-odds-title-block">
                                    <h5 class="match-odds-title">
                                      {{ runner.runner_name }}
                                    </h5>
                                    <div v-for="(m_runner_books, runner_bookIndex) in market.runner_books" v-if="m_runner_books.selection_id == runner.selection_id" :class="m_runner_books.pl > 0 ? 'match-odds-tag danger' : m_runner_books.pl < 0 ? 'match-odds-tag success' : 'match-odds-tag'">
                                      <span>{{ m_runner_books.pl | positive | addCommas }}</span>
                                    </div>
                                  </div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                  <h5 class="price-odd-title">
                                    {{  isset(data_runner.ex.availableToBack[2]) ? data_runner.ex.availableToBack[2].price : "" }}
                                  </h5>
                                  <div class="price-odd">
                                    {{ isset(data_runner.ex.availableToBack[2]) ? data_runner.ex.availableToBack[2].size : "" }}
                                  </div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                  <h5 class="price-odd-title">
                                    {{ isset(data_runner.ex.availableToBack[1]) ? data_runner.ex.availableToBack[1].price : "" }}
                                  </h5>
                                  <div class="price-odd">
                                    {{ isset(data_runner.ex.availableToBack[1]) ? data_runner.ex.availableToBack[1].size : "" }}
                                  </div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                  <h5 class="price-odd-title">
                                    {{ isset(data_runner.ex.availableToBack[0]) ? data_runner.ex.availableToBack[0].price : "" }}
                                  </h5>
                                  <div class="price-odd">
                                    {{ isset(data_runner.ex.availableToBack[0]) ? data_runner.ex.availableToBack[0].size : "" }}
                                  </div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                  <h5 class="price-odd-title">
                                    {{ isset(data_runner.ex.availableToLay[0]) ? data_runner.ex.availableToLay[0].price : "" }}
                                  </h5>
                                  <div class="price-odd">
                                    {{ isset(data_runner.ex.availableToLay[0]) ? data_runner.ex.availableToLay[0].size : "" }}
                                  </div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                  <h5 class="price-odd-title">
                                    {{ isset(data_runner.ex.availableToLay[1]) ? data_runner.ex.availableToLay[1].price : "" }}
                                  </h5>
                                  <div class="price-odd">
                                    {{ isset(data_runner.ex.availableToLay[1]) ? data_runner.ex.availableToLay[1].size : "" }}
                                  </div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                  <h5 class="price-odd-title">
                                    {{ isset(data_runner.ex.availableToLay[2]) ? data_runner.ex.availableToLay[2].price : "" }}
                                  </h5>
                                  <div class="price-odd">
                                    {{ isset(data_runner.ex.availableToLay[2]) ? data_runner.ex.availableToLay[2].size : "" }}
                                  </div>
                                </a>
                              </div>

                            </li>
                            <li v-if="currentSport.stake_size != null">
                              <div class="match-odds-outer d-flex justify-content-end flex-row min-max-text" style="margin-right: 3px; margin-top: 5px">
                                Min:
                                {{ currentSport.stake_size.min_match }} |&nbsp;
                                <span v-if="event.is_custom != 1">Max: {{ event.inplay ? (market.inplay_stake_limit != null ? market.inplay_stake_limit : (event.match_setting != null && event.match_setting.is_active && (event.match_setting.exch_stake_limit != null && event.match_setting.exch_stake_limit != '') ? event.match_setting.exch_stake_limit : (market.stake_limit != null && market.stake_limit != '' ? market.stake_limit :  currentSport.stake_size.max_match))) : (event.match_setting != null && event.match_setting.is_active && (event.match_setting.exch_stake_limit != null && event.match_setting.exch_stake_limit != '') ? event.match_setting.exch_stake_limit : (market.stake_limit != null && market.stake_limit != '' ? market.stake_limit :  currentSport.stake_size.max_match)) | kiloConverter }}</span>
                                <span v-if="event.is_custom == 1"> Max: {{ event.match_setting != null && event.match_setting.is_active && (event.match_setting.exch_stake_limit != null && event.match_setting.exch_stake_limit != '') ? event.match_setting.exch_stake_limit : (market.stake_limit != null && market.stake_limit != '' ? market.stake_limit :  currentSport.stake_size.max_match) | kiloConverter }}</span>
                              </div>
                            </li>
                          </ul>
                        </div>
                      </div>
                    </div>
                    <div class="tab-pane fade" id="bookmaker" role="tabpanel" aria-labelledby="bookmaker-tab">
                      <!-- All Bookmakers Odds -->
                      <div class="chart-body-outer" v-if="current_bookmakers.length > 0" v-for="bookmaker in current_bookmakers">
                        <div v-if="auth._role != 'is_director' && bookmaker.is_active">
                          <div class="chart-body-heading-block event-detail-manage-main-block">
                            <div class="event-detail-manage-block d-flex justify-content-between flex-row">
                              <h5>
                                {{ bookmaker.title }}
                                <span class="badge badge-primary">Bookmaker</span>
                              </h5>
                              <div class="chart-body-heading-details">
                                <div class="d-flex flex-row justify-content-between">
                                  <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                    <label class="button-toggle-wrap">
                                      <div class="chart-body-heading-two" style="padding-right: 0px">
                                        <button href="javascript:void(0)" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default book-btn" title="Booking"  @click="openBookOrBetModal(null, 'B', bookmaker.title, '', 'book-bookmaker-market-modal', true, bookmaker.id, null)">Book</button>
                                      </div>
                                    </label>
                                  </div>

                                  <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                    <label class="button-toggle-wrap">
                                      <div class="chart-body-heading-two" style="padding-right: 0px">
                                        <button href="javascript:void(0)" style="background: #4f687d" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default bet-btn" title="Bets"  @click="openBookOrBetModal(null, 'BB', bookmaker.title, '', 'bets-fancy-bookmaker-market-modal', true, bookmaker.id, null)">Bets</button>
                                      </div>
                                    </label>
                                  </div>
                                </div>
                              </div>
                            </div>
                          </div>
                          <div class="chart-body-heading-block">
                            <div class="chart-body-heading">
                              <div class="d-flex justify-content-between flex-row" style="justify-content: flex-end !important">
                                <h6 class="chart-body-heading chart-body-main-heading" style="display: none">
                                  <span>{{ bookmaker.title }}</span>
                                </h6>
                                <div class="chart-body-heading-details">
                                  <div class="d-flex flex-row justify-content-between">
                                    <div class="chart-body-heading chart-body-heading-outer d-flex flex-row justify-content-end">
                                      <div class="chart-body-heading-two">
                                        <span>Back</span>
                                      </div>
                                    </div>
                                    <div class="chart-body-heading chart-body-heading-outer-two chart-body-heading-outer d-flex flex-row">
                                      <div class="chart-body-heading-one">
                                        <span>Lay</span>
                                      </div>
                                    </div>
                                  </div>
                                </div>
                              </div>
                            </div>
                          </div>
                          <div v-if="bookmaker.msg != null && bookmaker.msg != '' && bookmaker.msg != ' '" class="chart-body-heading-block">
                            <div class="fancy-msg-block">
                              <div class="fancy-msg">
                                <marquee>{{ bookmaker.msg }}</marquee>
                              </div>
                            </div>
                          </div>
                          <div :class="bookmaker.is_sus ? 'chart-body suspended' : 'chart-body'" data-content="suspended">
                            <ul v-if="bookmaker.is_sus">
                              <li v-for="(bookmaker_runner_odd, bookmaker_runner_odd_index) in bookmaker.runner_odds">
                                <div class="match-odds-outer d-flex justify-content-between flex-row">
                                  <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                    <div class="match-odds-title-block">
                                      <h5 class="match-odds-title">
                                        {{ bookmaker_runner_odd.runner_name }}
                                      </h5>
                                      <div v-if="getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).is_exists" :class="getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).runner_book > 0 ? 'match-odds-tag danger' : getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).runner_book < 0 ? 'match-odds-tag success' : 'match-odds-tag'">
                                        <span>{{ getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).runner_book | positive | addCommas }}</span>
                                      </div>
                                    </div>
                                  </a>
                                  <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                    <h5 class="price-odd-title"></h5>
                                    <div class="price-odd"></div>
                                  </a>
                                  <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                    <h5 class="price-odd-title"></h5>
                                    <div class="price-odd"></div>
                                  </a>
                                  <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                    <h5 class="price-odd-title"></h5>
                                    <div class="price-odd"></div>
                                  </a>
                                  <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                    <h5 class="price-odd-title"></h5>
                                    <div class="price-odd"></div>
                                  </a>
                                  <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                    <h5 class="price-odd-title"></h5>
                                    <div class="price-odd"></div>
                                  </a>
                                  <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                    <h5 class="price-odd-title"></h5>
                                    <div class="price-odd"></div>
                                  </a>
                                </div>
                              </li>
                              <li>
                                <div class="match-odds-outer d-flex justify-content-end flex-row min-max-text" style="margin-right: 3px; margin-top: 5px">
                                  Min:
                                  {{ currentSport.stake_size.min_bookmaker }} | Max:
                                  {{ currentSport.stake_size.max_bookmaker | kiloConverter }}
                                </div>
                              </li>
                            </ul>
                            <ul v-else>
                              <li v-for="(bookmaker_runner_odd, bookmaker_runner_odd_index) in bookmaker.runner_odds">
                                <div class="match-odds-outer d-flex justify-content-between flex-row">
                                  <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                    <div class="match-odds-title-block">
                                      <h5 class="match-odds-title">
                                        {{ bookmaker_runner_odd.runner_name }}
                                      </h5>
                                      <div v-if="getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).is_exists" :class="getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).runner_book > 0 ? 'match-odds-tag danger' : getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).runner_book < 0 ? 'match-odds-tag success' : 'match-odds-tag'">
                                        <span>{{ getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).runner_book | positive | addCommas }}</span>
                                      </div>
                                    </div>
                                  </a>
                                  <div v-if="bookmaker_runner_odd.is_sus" class="price-odds-items-block d-flex justify-content-between flex-row suspended suspended-small" data-content="suspended">
                                    <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                  </div>
                                  <div v-else class="price-odds-items-block d-flex justify-content-between flex-row">
                                    <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                      <h5 class="price-odd-title">
                                        {{ bookmaker_runner_odd.back }}
                                      </h5>
                                      <div class="price-odd" v-if="bookmaker_runner_odd.back == '' && bookmaker_runner_odd.back == null"></div>
                                      <div class="price-odd" v-else>
                                        {{ (bookmaker.stake_limit != null && bookmaker.stake_limit != "" ? bookmaker.stake_limit : currentSport.stake_size.max_bookmaker) | kiloConverter }}
                                      </div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                      <h5 class="price-odd-title">
                                        {{ bookmaker_runner_odd.lay }}
                                      </h5>
                                      <div class="price-odd" v-if="bookmaker_runner_odd.lay == '' && bookmaker_runner_odd.lay == null"></div>
                                      <div class="price-odd" v-else>
                                        {{ (bookmaker.stake_limit != null && bookmaker.stake_limit != "" ? bookmaker.stake_limit : currentSport.stake_size.max_bookmaker) | kiloConverter }}
                                      </div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                  </div>
                                </div>
                              </li>
                              <li v-if="currentSport.stake_size != null">
                                <div class="match-odds-outer d-flex justify-content-end flex-row min-max-text" style="margin-right: 3px; margin-top: 5px">
                                  Min:
                                  {{ currentSport.stake_size.min_bookmaker }}
                                  |&nbsp;
                                  <span v-if="bookmaker.stake_limit != null && bookmaker.stake_limit != ''">
                                    Max:
                                    {{ bookmaker.stake_limit | kiloConverter }}
                                  </span>
                                  <span v-else>
                                    Max:
                                    {{ currentSport.stake_size.max_bookmaker | kiloConverter }}
                                  </span>
                                </div>
                              </li>
                            </ul>
                          </div>
                        </div>
                        <div v-if="auth._role == 'is_director'">
                          <div class="chart-body-heading-block event-detail-manage-main-block">
                            <div class="event-detail-manage-block d-flex justify-content-between flex-row">
                              <h5>{{ bookmaker.title }}</h5>
                              <div class="chart-body-heading-details">
                                <div class="d-flex flex-row justify-content-between">
                                  <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                    <h6>SUSPEND</h6>
                                    <label :for="'suspend_bookmaker_' + bookmaker.id" class="button-toggle-wrap">
                                      <input :id="'suspend_bookmaker_' + bookmaker.id" class="toggler" type="checkbox" v-model="bookmaker.is_sus" @change="manageMarkets('Bookmaker', 'suspend', bookmaker)" />
                                      <div class="button-toggle danger">
                                        <div class="handle">
                                          <div class="bars"></div>
                                        </div>
                                      </div>
                                    </label>
                                  </div>
                                  <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                    <h6>ACTIVE</h6>
                                    <label :for="'active_bookmaker_' + bookmaker.id" class="button-toggle-wrap">
                                      <input :id="'active_bookmaker_' + bookmaker.id" class="toggler" type="checkbox" v-model="bookmaker.is_active" @change="manageMarkets('Bookmaker', 'active', bookmaker)" />
                                      <div class="button-toggle">
                                        <div class="handle">
                                          <div class="bars"></div>
                                        </div>
                                      </div>
                                    </label>
                                  </div>
                                  <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                    <label class="button-toggle-wrap">
                                      <div class="chart-body-heading-two" style="padding-right: 0px">
                                        <button href="javascript:void(0)" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default book-btn" title="Booking"  @click="openBookOrBetModal(null, 'B', bookmaker.title, '', 'book-bookmaker-market-modal', true, bookmaker.id, null)">Book</button>
                                      </div>
                                    </label>
                                  </div>
                                  <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                    <label class="button-toggle-wrap">
                                      <div class="chart-body-heading-two" style="padding-right: 0px">
                                        <button href="javascript:void(0)" style="background: #4f687d" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default bet-btn" title="Bets"  @click="openBookOrBetModal(null, 'BB', bookmaker.title, '', 'bets-fancy-bookmaker-market-modal', true, bookmaker.id, null)">Bets</button>
                                      </div>
                                    </label>
                                  </div>
                                </div>
                              </div>
                            </div>
                          </div>
                          <div class="chart-body-heading-block">
                            <div class="chart-body-heading">
                              <div class="d-flex justify-content-between flex-row" style="justify-content: flex-end !important">
                                <h6 class="chart-body-heading chart-body-main-heading" style="display: none">
                                  <span>{{ bookmaker.title }}</span>
                                </h6>
                                <div class="chart-body-heading-details">
                                  <div class="d-flex flex-row justify-content-between">
                                    <div class="chart-body-heading chart-body-heading-outer d-flex flex-row justify-content-end">
                                      <div class="chart-body-heading-two">
                                        <span>Back</span>
                                      </div>
                                    </div>
                                    <div class="chart-body-heading chart-body-heading-outer-two chart-body-heading-outer d-flex flex-row">
                                      <div class="chart-body-heading-one">
                                        <span>Lay</span>
                                      </div>
                                    </div>
                                  </div>
                                </div>
                              </div>
                            </div>
                          </div>
                          <div :class="bookmaker.is_sus ? 'chart-body suspended' : 'chart-body'" data-content="suspended">
                            <ul v-if="bookmaker.is_sus">
                              <li v-for="(bookmaker_runner_odd, bookmaker_runner_odd_index) in bookmaker.runner_odds">
                                <div class="match-odds-outer d-flex justify-content-between flex-row">
                                  <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                    <div class="match-odds-title-block">
                                      <h5 class="match-odds-title">
                                        {{ bookmaker_runner_odd.runner_name }}
                                      </h5>
                                      <div v-if="getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).is_exists" :class="getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).runner_book > 0 ? 'match-odds-tag danger' : getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).runner_book < 0 ? 'match-odds-tag success' : 'match-odds-tag'">
                                        <span>{{ getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).runner_book | positive | addCommas }}</span>
                                      </div>
                                    </div>
                                  </a>
                                  <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                    <h5 class="price-odd-title"></h5>
                                    <div class="price-odd"></div>
                                  </a>
                                  <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                    <h5 class="price-odd-title"></h5>
                                    <div class="price-odd"></div>
                                  </a>
                                  <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                    <h5 class="price-odd-title"></h5>
                                    <div class="price-odd"></div>
                                  </a>
                                  <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                    <h5 class="price-odd-title"></h5>
                                    <div class="price-odd"></div>
                                  </a>
                                  <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                    <h5 class="price-odd-title"></h5>
                                    <div class="price-odd"></div>
                                  </a>
                                  <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                    <h5 class="price-odd-title"></h5>
                                    <div class="price-odd"></div>
                                  </a>
                                </div>
                              </li>
                              <li>
                                <div class="match-odds-outer d-flex justify-content-end flex-row min-max-text" style="margin-right: 3px; margin-top: 5px">
                                  Min:
                                  {{ currentSport.stake_size.min_bookmaker }} | Max:
                                  {{ currentSport.stake_size.max_bookmaker | kiloConverter }}
                                </div>
                              </li>
                            </ul>
                            <ul v-else>
                              <li v-for="(bookmaker_runner_odd, bookmaker_runner_odd_index) in bookmaker.runner_odds">
                                <div class="match-odds-outer d-flex justify-content-between flex-row">
                                  <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                    <div class="match-odds-title-block">
                                      <h5 class="match-odds-title">
                                        {{ bookmaker_runner_odd.runner_name }}
                                      </h5>
                                      <div v-if="getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).is_exists" :class="getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).runner_book > 0 ? 'match-odds-tag danger' : getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).runner_book < 0 ? 'match-odds-tag success' : 'match-odds-tag'">
                                        <span>{{ getBookmakerBooks(bookmaker.id,bookmaker_runner_odd.selection_id).runner_book | positive | addCommas }}</span>
                                      </div>
                                    </div>
                                  </a>
                                  <div v-if="bookmaker_runner_odd.is_sus" class="price-odds-items-block d-flex justify-content-between flex-row suspended suspended-small" data-content="suspended">
                                    <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                  </div>
                                  <div v-else class="price-odds-items-block d-flex justify-content-between flex-row">
                                    <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                      <h5 class="price-odd-title">
                                        {{ bookmaker_runner_odd.back }}
                                      </h5>
                                      <div class="price-odd" v-if="bookmaker_runner_odd.back == '' && bookmaker_runner_odd.back == null"></div>
                                      <div class="price-odd" v-else>
                                        {{ (bookmaker.stake_limit != null && bookmaker.stake_limit != "" ? bookmaker.stake_limit : currentSport.stake_size.max_bookmaker) | kiloConverter }}
                                      </div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                      <h5 class="price-odd-title">
                                        {{ bookmaker_runner_odd.lay }}
                                      </h5>
                                      <div class="price-odd" v-if="bookmaker_runner_odd.lay == '' && bookmaker_runner_odd.lay == null"></div>
                                      <div class="price-odd" v-else>
                                        {{ (bookmaker.stake_limit != null && bookmaker.stake_limit != "" ? bookmaker.stake_limit : currentSport.stake_size.max_bookmaker) | kiloConverter }}
                                      </div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                    <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                      <h5 class="price-odd-title"></h5>
                                      <div class="price-odd"></div>
                                    </a>
                                  </div>
                                </div>
                              </li>
                              <li v-if="currentSport.stake_size != null">
                                <div class="match-odds-outer d-flex justify-content-end flex-row min-max-text" style="margin-right: 3px; margin-top: 5px">
                                  Min:
                                  {{ currentSport.stake_size.min_bookmaker }}
                                  |&nbsp;
                                  <span v-if="bookmaker.stake_limit != null && bookmaker.stake_limit != ''">
                                    Max:
                                    {{ bookmaker.stake_limit | kiloConverter }}
                                  </span>
                                  <span v-else>
                                    Max:
                                    {{ currentSport.stake_size.max_bookmaker | kiloConverter }}
                                  </span>
                                </div>
                              </li>
                            </ul>
                          </div>
                        </div>
                      </div>
                    </div>
                    <div class="tab-pane fade" id="fancy" role="tabpanel" aria-labelledby="fancy-tab">
                      <!-- All Fancies Odds -->
                      <div class="row mobile" style="width: 100%; margin: 0">
                        <div class="col-lg-6 pad-0" v-if="normal_fancies != null && (auth._role == 'is_director' || normal_fancies.is_running) && normal_fancies.data.length > 0">
                          <div class="chart-body-outer fancy-main-block border-1">
                            <div class="chart-body-heading-block">
                              <div class="chart-body-heading">
                                <div class="d-flex justify-content-between flex-row">
                                  <h6 class="chart-body-heading chart-body-main-heading">
                                    <span>{{ normal_fancies.name }}</span>
                                  </h6>
                                  <div class="chart-body-heading-details">
                                    <div class="d-flex flex-row justify-content-between">
                                      <div class="chart-body-heading chart-body-heading-outer d-flex flex-row justify-content-end">
                                        <div class="chart-body-heading-two">
                                          <span>No</span>
                                        </div>
                                      </div>
                                      <div class="chart-body-heading chart-body-heading-outer-two chart-body-heading-outer d-flex flex-row">
                                        <div class="chart-body-heading-one">
                                          <span>Yes</span>
                                        </div>
                                      </div>
                                    </div>
                                  </div>
                                </div>
                              </div>
                            </div>
                            <div class="chart-body">
                              <ul>
                                <li v-for="(fancy, normal_fancies_index) in normal_fancies.data" :key="normal_fancies_index">
                                  <div v-if="auth._role != 'is_director'">
                                    <div class="chart-body-heading-block event-detail-manage-main-block mb-1">
                                      <div class="event-detail-manage-block d-flex justify-content-end flex-row">
                                        <div class="chart-body-heading-details">
                                          <div class="d-flex flex-row justify-content-end">
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <label class="button-toggle-wrap float-right">
                                                <div class="chart-body-heading-two" style="padding-right: 0px">
                                                  <button href="javascript:void(0)" style="background: #4f687d" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default bet-btn" title="Bets"  @click="openBookOrBetModal(null, 'BF', fancy.runner_name, '', 'bets-fancy-bookmaker-market-modal', true, null, fancy.id)">Bets</button>
                                                </div>
                                              </label>
                                            </div>
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="match-odds-outer d-flex justify-content-between flex-row">
                                      <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                        <div class="match-odds-title-block" @click.prevent="showFancyBook(fancy.id)">
                                          <span :data-tooltip="fancy.is_com ? 'Commission' : 'No Commission'" data-tooltip-location="right" :class="fancy.is_com ? 'badge border-success badge-pill info badge-border fancy-com-text' : 'badge border-warning badge-pill warning badge-border fancy-com-text'">{{
                                            fancy.is_com ? "CM" : "NC"
                                          }}</span>
                                          <h5 class="match-odds-title">
                                            {{ fancy.runner_name }}
                                          </h5>
                                          <div class="match-odds-tag danger">
                                            <span>{{ fancy.exposure}}</span>
                                          </div>
                                        </div>
                                      </a>
                                      <div class="price-odds-items-block d-flex justify-content-between flex-row">
                                        <div v-if="fancy.is_sus" class="d-flex justify-content-between flex-row mr-8 suspended suspended-small pos-rel" data-content="suspended">
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                        </div>
                                        <div v-else class="d-flex justify-content-between flex-row mr-8 pos-rel">
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.no_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ fancy.no_odd != "" && fancy.no_odd != null ? fancy.no_bhav : "" }}
                                            </div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.yes_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ fancy.yes_odd != "" && fancy.yes_odd != null ? fancy.yes_bhav : "" }}
                                            </div>
                                          </a>
                                        </div>
                                        <div class="price-odds-min-max-block text-right min-max-text">
                                          <div class="price-odds-min-max-title">
                                            Min:
                                            {{ fancy.min_stake != null && fancy.min_stake != "" ? fancy.min_stake : currentSport.stake_size.min_fancy }}
                                          </div>
                                          <div class="price-odds-min-max-title">
                                            Max:
                                            {{ (fancy.max_stake != null && fancy.max_stake != "" ? fancy.max_stake : currentSport.stake_size.max_fancy) | kiloConverter }}
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="fancy-msg-block" v-if="fancy.msg != null && fancy.msg != ''">
                                      <div class="fancy-msg">
                                        <marquee>{{ fancy.msg }}</marquee>
                                      </div>
                                    </div>
                                  </div>
                                  <div v-else>
                                    <div class="chart-body-heading-block event-detail-manage-main-block">
                                      <div class="event-detail-manage-block d-flex justify-content-between flex-row">
                                        <h5>MANAGE</h5>
                                        <div class="chart-body-heading-details">
                                          <div class="d-flex flex-row justify-content-between">
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <h6>SUSPEND</h6>
                                              <label :for="'suspend_fancy_' + fancy.id" class="button-toggle-wrap">
                                                <input :id="'suspend_fancy_' + fancy.id" class="toggler" type="checkbox" v-model="fancy.is_sus" @change="manageMarkets('Fancy', 'suspend', fancy)" />
                                                <div class="button-toggle danger">
                                                  <div class="handle">
                                                    <div class="bars"></div>
                                                  </div>
                                                </div>
                                              </label>
                                            </div>
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <h6>ACTIVE</h6>
                                              <label :for="'active_fancy_' + fancy.id" class="button-toggle-wrap">
                                                <input :id="'active_fancy_' + fancy.id" class="toggler" type="checkbox" v-model="fancy.is_active" @change="manageMarkets('Fancy', 'active', fancy)" />
                                                <div class="button-toggle">
                                                  <div class="handle">
                                                    <div class="bars"></div>
                                                  </div>
                                                </div>
                                              </label>
                                            </div>
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <label class="button-toggle-wrap">
                                                <div class="chart-body-heading-two" style="padding-right: 0px">
                                                  <button href="javascript:void(0)" style="background: #4f687d" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default bet-btn" title="Bets"  @click="openBookOrBetModal(null, 'BF', fancy.runner_name, '', 'bets-fancy-bookmaker-market-modal', true, null, fancy.id)">Bets</button>
                                                </div>
                                              </label>
                                            </div>
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="match-odds-outer d-flex justify-content-between flex-row">
                                      <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                        <div class="match-odds-title-block" @click.prevent="showFancyBook(fancy.id)">
                                          <span :data-tooltip="fancy.is_com ? 'Commission' : 'No Commission'" data-tooltip-location="right" :class="fancy.is_com ? 'badge border-success badge-pill info badge-border fancy-com-text' : 'badge border-warning badge-pill warning badge-border fancy-com-text'">{{
                                            fancy.is_com ? "CM" : "NC"
                                          }}</span>
                                          <h5 class="match-odds-title">
                                            {{ fancy.runner_name }}
                                          </h5>
                                          <div class="match-odds-tag danger">
                                            <span>{{ fancy.exposure}}</span>
                                          </div>
                                        </div>
                                      </a>
                                      <div class="price-odds-items-block d-flex justify-content-between flex-row">
                                        <div v-if="fancy.is_sus" class="d-flex justify-content-between flex-row mr-8 suspended suspended-small pos-rel" data-content="suspended">
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                        </div>
                                        <div v-else class="d-flex justify-content-between flex-row mr-8 pos-rel">
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.no_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ fancy.no_odd != "" && fancy.no_odd != null ? fancy.no_bhav : "" }}
                                            </div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.yes_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ fancy.yes_odd != "" && fancy.yes_odd != null ? fancy.yes_bhav : "" }}
                                            </div>
                                          </a>
                                        </div>
                                        <div class="price-odds-min-max-block text-right min-max-text">
                                          <div class="price-odds-min-max-title">
                                            Min:
                                            {{ fancy.min_stake != null && fancy.min_stake != "" ? fancy.min_stake : currentSport.stake_size.min_fancy }}
                                          </div>
                                          <div class="price-odds-min-max-title">
                                            Max:
                                            {{ (fancy.max_stake != null && fancy.max_stake != "" ? fancy.max_stake : currentSport.stake_size.max_fancy) | kiloConverter }}
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="fancy-msg-block" v-if="fancy.msg != null && fancy.msg != ''">
                                      <div class="fancy-msg">
                                        <marquee>{{ fancy.msg }}</marquee>
                                      </div>
                                    </div>
                                  </div>
                                </li>
                              </ul>
                            </div>
                          </div>
                        </div>
                        <div class="col-lg-6 pad-0">
                          <div class="chart-body-outer fancy-main-block border-1" v-if="ball_by_ball_fancies != null && (auth._role == 'is_director' || ball_by_ball_fancies.is_running) && ball_by_ball_fancies.data.length > 0">
                            <div class="chart-body-heading-block">
                              <div class="chart-body-heading">
                                <div class="d-flex justify-content-between flex-row">
                                  <h6 class="chart-body-heading chart-body-main-heading">
                                    <span>{{ ball_by_ball_fancies.name }}</span>
                                  </h6>
                                  <div class="chart-body-heading-details">
                                    <div class="d-flex flex-row justify-content-between">
                                      <div class="chart-body-heading chart-body-heading-outer d-flex flex-row justify-content-end">
                                        <div class="chart-body-heading-two">
                                          <span>No</span>
                                        </div>
                                      </div>
                                      <div class="chart-body-heading chart-body-heading-outer-two chart-body-heading-outer d-flex flex-row">
                                        <div class="chart-body-heading-one">
                                          <span>Yes</span>
                                        </div>
                                      </div>
                                    </div>
                                  </div>
                                </div>
                              </div>
                            </div>
                            <div class="chart-body">
                              <ul>
                                <li v-for="(fancy, ball_by_ball_fancies_index) in ball_by_ball_fancies.data" :key="ball_by_ball_fancies_index">
                                  <div v-if="auth._role != 'is_director'">
                                    <div class="chart-body-heading-block event-detail-manage-main-block mb-1">
                                      <div class="event-detail-manage-block d-flex justify-content-end flex-row">
                                        <div class="chart-body-heading-details">
                                          <div class="d-flex flex-row justify-content-end">
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <label class="button-toggle-wrap float-right">
                                                <div class="chart-body-heading-two" style="padding-right: 0px">
                                                  <button href="javascript:void(0)" style="background: #4f687d" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default bet-btn" title="Bets"  @click="openBookOrBetModal(null, 'BF', fancy.runner_name, '', 'bets-fancy-bookmaker-market-modal', true, null, fancy.id)">Bets</button>
                                                </div>
                                              </label>
                                            </div>
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="match-odds-outer d-flex justify-content-between flex-row">
                                      <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                        <div class="match-odds-title-block" @click.prevent="showFancyBook(fancy.id)">
                                          <span :data-tooltip="fancy.is_com ? 'Commission' : 'No Commission'" data-tooltip-location="right" :class="fancy.is_com ? 'badge border-success badge-pill info badge-border fancy-com-text' : 'badge border-warning badge-pill warning badge-border fancy-com-text'">{{
                                            fancy.is_com ? "CM" : "NC"
                                          }}</span>
                                          <h5 class="match-odds-title">
                                            {{ fancy.runner_name }}
                                          </h5>
                                          <div class="match-odds-tag danger">
                                            <span>{{ fancy.exposure }}</span>
                                          </div>
                                        </div>
                                      </a>
                                      <div class="price-odds-items-block d-flex justify-content-between flex-row">
                                        <div v-if="fancy.is_sus" class="d-flex justify-content-between flex-row mr-8 suspended suspended-small pos-rel" data-content="suspended">
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                        </div>
                                        <div v-else class="d-flex justify-content-between flex-row mr-8 pos-rel">
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.no_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ fancy.no_odd != "" && fancy.no_odd != null ? fancy.no_bhav : "" }}
                                            </div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.yes_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ fancy.yes_odd != "" && fancy.yes_odd != null ? fancy.yes_bhav : "" }}
                                            </div>
                                          </a>
                                        </div>
                                        <div class="price-odds-min-max-block text-right min-max-text">
                                          <div class="price-odds-min-max-title">
                                            Min:
                                            {{ fancy.min_stake != null && fancy.min_stake != "" ? fancy.min_stake : currentSport.stake_size.min_fancy }}
                                          </div>
                                          <div class="price-odds-min-max-title">
                                            Max:
                                            {{ (fancy.max_stake != null && fancy.max_stake != "" ? fancy.max_stake : currentSport.stake_size.max_fancy) | kiloConverter }}
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="fancy-msg-block" v-if="fancy.msg != null && fancy.msg != ''">
                                      <div class="fancy-msg">
                                        <marquee>{{ fancy.msg }}</marquee>
                                      </div>
                                    </div>
                                  </div>
                                  <div v-else>
                                    <div class="chart-body-heading-block event-detail-manage-main-block">
                                      <div class="event-detail-manage-block d-flex justify-content-between flex-row">
                                        <h5>MANAGE</h5>
                                        <div class="chart-body-heading-details">
                                          <div class="d-flex flex-row justify-content-between">
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <h6>SUSPEND</h6>
                                              <label :for="'suspend_fancy_' + fancy.id" class="button-toggle-wrap">
                                                <input :id="'suspend_fancy_' + fancy.id" class="toggler" type="checkbox" v-model="fancy.is_sus" @change="manageMarkets('Fancy', 'suspend', fancy)" />
                                                <div class="button-toggle danger">
                                                  <div class="handle">
                                                    <div class="bars"></div>
                                                  </div>
                                                </div>
                                              </label>
                                            </div>
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <h6>ACTIVE</h6>
                                              <label :for="'active_fancy_' + fancy.id" class="button-toggle-wrap">
                                                <input :id="'active_fancy_' + fancy.id" class="toggler" type="checkbox" v-model="fancy.is_active" @change="manageMarkets('Fancy', 'active', fancy)" />
                                                <div class="button-toggle">
                                                  <div class="handle">
                                                    <div class="bars"></div>
                                                  </div>
                                                </div>
                                              </label>
                                            </div>
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <label class="button-toggle-wrap">
                                                <div class="chart-body-heading-two" style="padding-right: 0px">
                                                  <button href="javascript:void(0)" style="background: #4f687d" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default bet-btn" title="Bets"  @click="openBookOrBetModal(null, 'BF', fancy.runner_name, '', 'bets-fancy-bookmaker-market-modal', true, null, fancy.id)">Bets</button>
                                                </div>
                                              </label>
                                            </div>
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="match-odds-outer d-flex justify-content-between flex-row">
                                      <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                        <div class="match-odds-title-block" @click.prevent="showFancyBook(fancy.id)">
                                          <span :data-tooltip="fancy.is_com ? 'Commission' : 'No Commission'" data-tooltip-location="right" :class="fancy.is_com ? 'badge border-success badge-pill info badge-border fancy-com-text' : 'badge border-warning badge-pill warning badge-border fancy-com-text'">{{
                                            fancy.is_com ? "CM" : "NC"
                                          }}</span>
                                          <h5 class="match-odds-title">
                                            {{ fancy.runner_name }}
                                          </h5>
                                          <div class="match-odds-tag danger">
                                            <span>{{ fancy.exposure}}</span>
                                          </div>
                                        </div>
                                      </a>
                                      <div class="price-odds-items-block d-flex justify-content-between flex-row">
                                        <div v-if="fancy.is_sus" class="d-flex justify-content-between flex-row mr-8 suspended suspended-small pos-rel" data-content="suspended">
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                        </div>
                                        <div v-else class="d-flex justify-content-between flex-row mr-8 pos-rel">
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.no_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ fancy.no_odd != "" && fancy.no_odd != null ? fancy.no_bhav : "" }}
                                            </div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.yes_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ fancy.yes_odd != "" && fancy.yes_odd != null ? fancy.yes_bhav : "" }}
                                            </div>
                                          </a>
                                        </div>
                                        <div class="price-odds-min-max-block text-right min-max-text">
                                          <div class="price-odds-min-max-title">
                                            Min:
                                            {{ fancy.min_stake != null && fancy.min_stake != "" ? fancy.min_stake : currentSport.stake_size.min_fancy }}
                                          </div>
                                          <div class="price-odds-min-max-title">
                                            Max:
                                            {{ (fancy.max_stake != null && fancy.max_stake != "" ? fancy.max_stake : currentSport.stake_size.max_fancy) | kiloConverter }}
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="fancy-msg-block" v-if="fancy.msg != null && fancy.msg != ''">
                                      <div class="fancy-msg">
                                        <marquee>{{ fancy.msg }}</marquee>
                                      </div>
                                    </div>
                                  </div>
                                </li>
                              </ul>
                            </div>
                          </div>
                          <div class="chart-body-outer fancy-main-block border-1" v-if="khado_fancies != null && (auth._role == 'is_director' || khado_fancies.is_running) && khado_fancies.data.length > 0">
                            <div class="chart-body-heading-block">
                              <div class="chart-body-heading">
                                <div class="d-flex justify-content-between flex-row">
                                  <h6 class="chart-body-heading chart-body-main-heading">
                                    <span>{{ khado_fancies.name }}</span>
                                  </h6>
                                  <div class="chart-body-heading-details">
                                    <div class="d-flex flex-row justify-content-between">
                                      <div class="chart-body-heading chart-body-heading-outer d-flex flex-row justify-content-end">
                                        <div class="chart-body-heading-two">
                                          <span>Back</span>
                                        </div>
                                      </div>
                                      <div class="chart-body-heading chart-body-heading-outer-two chart-body-heading-outer d-flex flex-row">
                                        <div class="chart-body-heading-one">
                                          <span>Lay</span>
                                        </div>
                                      </div>
                                    </div>
                                  </div>
                                </div>
                              </div>
                            </div>
                            <div class="chart-body">
                              <ul>
                                <li v-for="(fancy, khado_fancies_index) in khado_fancies.data" :key="khado_fancies_index">
                                  <div v-if="auth._role != 'is_director'">
                                    <div class="chart-body-heading-block event-detail-manage-main-block mb-1">
                                      <div class="event-detail-manage-block d-flex justify-content-end flex-row">
                                        <div class="chart-body-heading-details">
                                          <div class="d-flex flex-row justify-content-end">
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <label class="button-toggle-wrap float-right">
                                                <div class="chart-body-heading-two" style="padding-right: 0px">
                                                  <button href="javascript:void(0)" style="background: #4f687d" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default bet-btn" title="Bets"  @click="openBookOrBetModal(null, 'BF', fancy.runner_name, '', 'bets-fancy-bookmaker-market-modal', true, null, fancy.id)">Bets</button>
                                                </div>
                                              </label>
                                            </div>
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="match-odds-outer d-flex justify-content-between flex-row">
                                      <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                        <div class="match-odds-title-block" @click.prevent="showFancyBook(fancy.id)">
                                          <span :data-tooltip="fancy.is_com ? 'Commission' : 'No Commission'" data-tooltip-location="right" :class="fancy.is_com ? 'badge border-success badge-pill info badge-border fancy-com-text' : 'badge border-warning badge-pill warning badge-border fancy-com-text'">{{
                                            fancy.is_com ? "CM" : "NC"
                                          }}</span>
                                          <h5 class="match-odds-title">
                                            {{ fancy.runner_name }}
                                          </h5>
                                          <div class="match-odds-tag danger">
                                            <span>{{ fancy.exposure}}</span>
                                          </div>
                                        </div>
                                      </a>
                                      <div class="price-odds-items-block d-flex justify-content-between flex-row">
                                        <div v-if="fancy.is_sus" class="d-flex justify-content-between flex-row mr-8 suspended suspended-small pos-rel" data-content="suspended">
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                        </div>
                                        <div v-else class="d-flex justify-content-between flex-row mr-8 pos-rel">
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price" ss="price-odd-title"
                                            ><h5>{{ fancy.yes_odd }}</h5>
                                            <div class="price-odd">
                                              {{ fancy.yes_odd != "" && fancy.yes_odd != null ? fancy.yes_bhav : "" }}
                                            </div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                        </div>
                                        <div class="price-odds-min-max-block text-right min-max-text">
                                          <div class="price-odds-min-max-title">
                                            Min:
                                            {{ fancy.min_stake != null && fancy.min_stake != "" ? fancy.min_stake : currentSport.stake_size.min_fancy }}
                                          </div>
                                          <div class="price-odds-min-max-title">
                                            Max:
                                            {{ (fancy.max_stake != null && fancy.max_stake != "" ? fancy.max_stake : currentSport.stake_size.max_fancy) | kiloConverter }}
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="fancy-msg-block" v-if="fancy.msg != null && fancy.msg != ''">
                                      <div class="fancy-msg">
                                        <marquee>{{ fancy.msg }}</marquee>
                                      </div>
                                    </div>
                                  </div>
                                  <div v-else>
                                    <div class="chart-body-heading-block event-detail-manage-main-block">
                                      <div class="event-detail-manage-block d-flex justify-content-between flex-row">
                                        <h5>MANAGE</h5>
                                        <div class="chart-body-heading-details">
                                          <div class="d-flex flex-row justify-content-between">
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <h6>SUSPEND</h6>
                                              <label :for="'suspend_fancy_' + fancy.id" class="button-toggle-wrap">
                                                <input :id="'suspend_fancy_' + fancy.id" class="toggler" type="checkbox" v-model="fancy.is_sus" @change="manageMarkets('Fancy', 'suspend', fancy)" />
                                                <div class="button-toggle danger">
                                                  <div class="handle">
                                                    <div class="bars"></div>
                                                  </div>
                                                </div>
                                              </label>
                                            </div>
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <h6>ACTIVE</h6>
                                              <label :for="'active_fancy_' + fancy.id" class="button-toggle-wrap">
                                                <input :id="'active_fancy_' + fancy.id" class="toggler" type="checkbox" v-model="fancy.is_active" @change="manageMarkets('Fancy', 'active', fancy)" />
                                                <div class="button-toggle">
                                                  <div class="handle">
                                                    <div class="bars"></div>
                                                  </div>
                                                </div>
                                              </label>
                                            </div>
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <label class="button-toggle-wrap">
                                                <div class="chart-body-heading-two" style="padding-right: 0px">
                                                  <button href="javascript:void(0)" style="background: #4f687d" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default bet-btn" title="Bets"  @click="openBookOrBetModal(null, 'BF', fancy.runner_name, '', 'bets-fancy-bookmaker-market-modal', true, null, fancy.id)">Bets</button>
                                                </div>
                                              </label>
                                            </div>
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="match-odds-outer d-flex justify-content-between flex-row">
                                      <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                        <div class="match-odds-title-block" @click.prevent="showFancyBook(fancy.id)">
                                          <span :data-tooltip="fancy.is_com ? 'Commission' : 'No Commission'" data-tooltip-location="right" :class="fancy.is_com ? 'badge border-success badge-pill info badge-border fancy-com-text' : 'badge border-warning badge-pill warning badge-border fancy-com-text'">{{
                                            fancy.is_com ? "CM" : "NC"
                                          }}</span>
                                          <h5 class="match-odds-title">
                                            {{ fancy.runner_name }}
                                          </h5>
                                          <div class="match-odds-tag danger">
                                            <span>{{ fancy.exposure}}</span>
                                          </div>
                                        </div>
                                      </a>
                                      <div class="price-odds-items-block d-flex justify-content-between flex-row">
                                        <div v-if="fancy.is_sus" class="d-flex justify-content-between flex-row mr-8 suspended suspended-small pos-rel" data-content="suspended">
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                        </div>
                                        <div v-else class="d-flex justify-content-between flex-row mr-8 pos-rel">
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price" ss="price-odd-title"
                                            ><h5>{{ fancy.yes_odd }}</h5>
                                            <div class="price-odd">
                                              {{ fancy.yes_odd != "" && fancy.yes_odd != null ? fancy.yes_bhav : "" }}
                                            </div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                        </div>
                                        <div class="price-odds-min-max-block text-right min-max-text">
                                          <div class="price-odds-min-max-title">
                                            Min:
                                            {{ fancy.min_stake != null && fancy.min_stake != "" ? fancy.min_stake : currentSport.stake_size.min_fancy }}
                                          </div>
                                          <div class="price-odds-min-max-title">
                                            Max:
                                            {{ (fancy.max_stake != null && fancy.max_stake != "" ? fancy.max_stake : currentSport.stake_size.max_fancy) | kiloConverter }}
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="fancy-msg-block" v-if="fancy.msg != null && fancy.msg != ''">
                                      <div class="fancy-msg">
                                        <marquee>{{ fancy.msg }}</marquee>
                                      </div>
                                    </div>
                                  </div>
                                </li>
                              </ul>
                            </div>
                          </div>
                          <div class="chart-body-outer fancy-main-block border-1" v-if="meter_fancies != null && (auth._role == 'is_director' || meter_fancies.is_running) && meter_fancies.data.length > 0">
                            <div class="chart-body-heading-block">
                              <div class="chart-body-heading">
                                <div class="d-flex justify-content-between flex-row">
                                  <h6 class="chart-body-heading chart-body-main-heading">
                                    <span>{{ meter_fancies.name }}</span>
                                  </h6>
                                  <div class="chart-body-heading-details">
                                    <div class="d-flex flex-row justify-content-between">
                                      <div class="chart-body-heading chart-body-heading-outer d-flex flex-row justify-content-end">
                                        <div class="chart-body-heading-two">
                                          <span>No</span>
                                        </div>
                                      </div>
                                      <div class="chart-body-heading chart-body-heading-outer-two chart-body-heading-outer d-flex flex-row">
                                        <div class="chart-body-heading-one">
                                          <span>Yes</span>
                                        </div>
                                      </div>
                                    </div>
                                  </div>
                                </div>
                              </div>
                            </div>
                            <div class="chart-body">
                              <ul>
                                <li v-for="(fancy, meter_fancies_index) in meter_fancies.data" :key="meter_fancies_index">
                                  <div v-if="auth._role != 'is_director'">
                                    <div class="chart-body-heading-block event-detail-manage-main-block mb-1">
                                      <div class="event-detail-manage-block d-flex justify-content-end flex-row">
                                        <div class="chart-body-heading-details">
                                          <div class="d-flex flex-row justify-content-end">
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <label class="button-toggle-wrap float-right">
                                                <div class="chart-body-heading-two" style="padding-right: 0px">
                                                  <button href="javascript:void(0)" style="background: #4f687d" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default bet-btn" title="Bets"  @click="openBookOrBetModal(null, 'BF', fancy.runner_name, '', 'bets-fancy-bookmaker-market-modal', true, null, fancy.id)">Bets</button>
                                                </div>
                                              </label>
                                            </div>
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="match-odds-outer d-flex justify-content-between flex-row">
                                      <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                        <div class="match-odds-title-block" @click.prevent="showFancyBook(fancy.id)">
                                          <span :data-tooltip="fancy.is_com ? 'Commission' : 'No Commission'" data-tooltip-location="right" :class="fancy.is_com ? 'badge border-success badge-pill info badge-border fancy-com-text' : 'badge border-warning badge-pill warning badge-border fancy-com-text'">{{
                                            fancy.is_com ? "CM" : "NC"
                                          }}</span>
                                          <h5 class="match-odds-title">
                                            {{ fancy.runner_name }}
                                          </h5>
                                          <div class="match-odds-tag danger">
                                            <span>{{ fancy.exposure}}</span>
                                          </div>
                                        </div>
                                      </a>
                                      <div class="price-odds-items-block d-flex justify-content-between flex-row">
                                        <div v-if="fancy.is_sus" class="d-flex justify-content-between flex-row mr-8 suspended suspended-small pos-rel" data-content="suspended">
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                        </div>
                                        <div v-else class="d-flex justify-content-between flex-row mr-8 pos-rel">
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.no_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ fancy.no_odd != "" && fancy.no_odd != null ? fancy.no_bhav : "" }}
                                            </div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.yes_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ fancy.yes_odd != "" && fancy.yes_odd != null ? fancy.yes_bhav : "" }}
                                            </div>
                                          </a>
                                        </div>
                                        <div class="price-odds-min-max-block text-right min-max-text">
                                          <div class="price-odds-min-max-title">
                                            Min:
                                            {{ fancy.min_stake != null && fancy.min_stake != "" ? fancy.min_stake : currentSport.stake_size.min_fancy }}
                                          </div>
                                          <div class="price-odds-min-max-title">
                                            Max:
                                            {{ (fancy.max_stake != null && fancy.max_stake != "" ? fancy.max_stake : currentSport.stake_size.max_fancy) | kiloConverter }}
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="fancy-msg-block" v-if="fancy.msg != null && fancy.msg != ''">
                                      <div class="fancy-msg">
                                        <marquee>{{ fancy.msg }}</marquee>
                                      </div>
                                    </div>
                                  </div>
                                  <div v-else>
                                    <div class="chart-body-heading-block event-detail-manage-main-block">
                                      <div class="event-detail-manage-block d-flex justify-content-between flex-row">
                                        <h5>MANAGE</h5>
                                        <div class="chart-body-heading-details">
                                          <div class="d-flex flex-row justify-content-between">
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <h6>SUSPEND</h6>
                                              <label :for="'suspend_fancy_' + fancy.id" class="button-toggle-wrap">
                                                <input :id="'suspend_fancy_' + fancy.id" class="toggler" type="checkbox" v-model="fancy.is_sus" @change="manageMarkets('Fancy', 'suspend', fancy)" />
                                                <div class="button-toggle danger">
                                                  <div class="handle">
                                                    <div class="bars"></div>
                                                  </div>
                                                </div>
                                              </label>
                                            </div>
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <h6>ACTIVE</h6>
                                              <label :for="'active_fancy_' + fancy.id" class="button-toggle-wrap">
                                                <input :id="'active_fancy_' + fancy.id" class="toggler" type="checkbox" v-model="fancy.is_active" @change="manageMarkets('Fancy', 'active', fancy)" />
                                                <div class="button-toggle">
                                                  <div class="handle">
                                                    <div class="bars"></div>
                                                  </div>
                                                </div>
                                              </label>
                                            </div>
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <label class="button-toggle-wrap">
                                                <div class="chart-body-heading-two" style="padding-right: 0px">
                                                  <button href="javascript:void(0)" style="background: #4f687d" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default bet-btn" title="Bets"  @click="openBookOrBetModal(null, 'BF', fancy.runner_name, '', 'bets-fancy-bookmaker-market-modal', true, null, fancy.id)">Bets</button>
                                                </div>
                                              </label>
                                            </div>
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="match-odds-outer d-flex justify-content-between flex-row">
                                      <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                        <div class="match-odds-title-block" @click.prevent="showFancyBook(fancy.id)">
                                          <span :data-tooltip="fancy.is_com ? 'Commission' : 'No Commission'" data-tooltip-location="right" :class="fancy.is_com ? 'badge border-success badge-pill info badge-border fancy-com-text' : 'badge border-warning badge-pill warning badge-border fancy-com-text'">{{
                                            fancy.is_com ? "CM" : "NC"
                                          }}</span>
                                          <h5 class="match-odds-title">
                                            {{ fancy.runner_name }}
                                          </h5>
                                          <div class="match-odds-tag danger">
                                            <span>{{ fancy.exposure}}</span>
                                          </div>
                                        </div>
                                      </a>
                                      <div class="price-odds-items-block d-flex justify-content-between flex-row">
                                        <div v-if="fancy.is_sus" class="d-flex justify-content-between flex-row mr-8 suspended suspended-small pos-rel" data-content="suspended">
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                        </div>
                                        <div v-else class="d-flex justify-content-between flex-row mr-8 pos-rel">
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.no_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ fancy.no_odd != "" && fancy.no_odd != null ? fancy.no_bhav : "" }}
                                            </div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.yes_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ fancy.yes_odd != "" && fancy.yes_odd != null ? fancy.yes_bhav : "" }}
                                            </div>
                                          </a>
                                        </div>
                                        <div class="price-odds-min-max-block text-right min-max-text">
                                          <div class="price-odds-min-max-title">
                                            Min:
                                            {{ fancy.min_stake != null && fancy.min_stake != "" ? fancy.min_stake : currentSport.stake_size.min_fancy }}
                                          </div>
                                          <div class="price-odds-min-max-title">
                                            Max:
                                            {{ (fancy.max_stake != null && fancy.max_stake != "" ? fancy.max_stake : currentSport.stake_size.max_fancy) | kiloConverter }}
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="fancy-msg-block" v-if="fancy.msg != null && fancy.msg != ''">
                                      <div class="fancy-msg">
                                        <marquee>{{ fancy.msg }}</marquee>
                                      </div>
                                    </div>
                                  </div>
                                </li>
                              </ul>
                            </div>
                          </div>
                          <div class="chart-body-outer fancy-main-block border-1" v-if="fancy_1_fancies != null && (auth._role == 'is_director' || fancy_1_fancies.is_running) && fancy_1_fancies.data.length > 0">
                            <div class="chart-body-heading-block">
                              <div class="chart-body-heading">
                                <div class="d-flex justify-content-between flex-row">
                                  <h6 class="chart-body-heading chart-body-main-heading">
                                    <span>{{ fancy_1_fancies.name }}</span>
                                  </h6>
                                  <div class="chart-body-heading-details">
                                    <div class="d-flex flex-row justify-content-between">
                                      <div class="chart-body-heading chart-body-heading-outer d-flex flex-row justify-content-end">
                                        <div class="chart-body-heading-two">
                                          <span>Back</span>
                                        </div>
                                      </div>
                                      <div class="chart-body-heading chart-body-heading-outer-two chart-body-heading-outer d-flex flex-row">
                                        <div class="chart-body-heading-one">
                                          <span>Lay</span>
                                        </div>
                                      </div>
                                    </div>
                                  </div>
                                </div>
                              </div>
                            </div>
                            <div class="chart-body">
                              <ul>
                                <li v-for="(fancy, fancy_1_fancies_index) in fancy_1_fancies.data" :key="fancy_1_fancies_index">
                                  <div v-if="auth._role != 'is_director'">
                                    <div class="chart-body-heading-block event-detail-manage-main-block mb-1">
                                      <div class="event-detail-manage-block d-flex justify-content-end flex-row">
                                        <div class="chart-body-heading-details">
                                          <div class="d-flex flex-row justify-content-end">
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <label class="button-toggle-wrap float-right">
                                                <div class="chart-body-heading-two" style="padding-right: 0px">
                                                  <button href="javascript:void(0)" style="background: #4f687d" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default bet-btn" title="Bets"  @click="openBookOrBetModal(null, 'BF', fancy.runner_name, '', 'bets-fancy-bookmaker-market-modal', true, null, fancy.id)">Bets</button>
                                                </div>
                                              </label>
                                            </div>
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="match-odds-outer d-flex justify-content-between flex-row">
                                      <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                        <div class="match-odds-title-block">
                                          <span :data-tooltip="fancy.is_com ? 'Commission' : 'No Commission'" data-tooltip-location="right" :class="fancy.is_com ? 'badge border-success badge-pill info badge-border fancy-com-text' : 'badge border-warning badge-pill warning badge-border fancy-com-text'">{{
                                            fancy.is_com ? "CM" : "NC"
                                          }}</span>
                                          <h5 class="match-odds-title">
                                            {{ fancy.runner_name }}
                                          </h5>
                                          <div class="match-odds-tag danger">
                                            <span>{{ fancy.exposure}}</span>
                                          </div>
                                        </div>
                                      </a>
                                      <div class="price-odds-items-block d-flex justify-content-between flex-row">
                                        <div v-if="fancy.is_sus" class="d-flex justify-content-between flex-row mr-8 suspended suspended-small pos-rel" data-content="suspended">
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                        </div>
                                        <div v-else class="d-flex justify-content-between flex-row mr-8 pos-rel">
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.yes_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ (fancy.yes_odd != "" && fancy.yes_odd != null ? (fancy.max_stake != null && fancy.max_stake != "" ? fancy.max_stake : currentSport.stake_size.max_fancy) : "") | kiloConverter }}
                                            </div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.no_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ (fancy.no_odd != "" && fancy.no_odd != null ? (fancy.max_stake != null && fancy.max_stake != "" ? fancy.max_stake : currentSport.stake_size.max_fancy) : "") | kiloConverter }}
                                            </div>
                                          </a>
                                        </div>
                                        <div class="price-odds-min-max-block text-right min-max-text">
                                          <div class="price-odds-min-max-title">
                                            Min:
                                            {{ fancy.min_stake != null && fancy.min_stake != "" ? fancy.min_stake : currentSport.stake_size.min_fancy }}
                                          </div>
                                          <div class="price-odds-min-max-title">
                                            Max:
                                            {{ (fancy.max_stake != null && fancy.max_stake != "" ? fancy.max_stake : currentSport.stake_size.max_fancy) | kiloConverter }}
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="fancy-msg-block" v-if="fancy.msg != null && fancy.msg != ''">
                                      <div class="fancy-msg">
                                        <marquee>{{ fancy.msg }}</marquee>
                                      </div>
                                    </div>
                                  </div>
                                  <div v-else>
                                    <div class="chart-body-heading-block event-detail-manage-main-block">
                                      <div class="event-detail-manage-block d-flex justify-content-between flex-row">
                                        <h5>MANAGE</h5>
                                        <div class="chart-body-heading-details">
                                          <div class="d-flex flex-row justify-content-between">
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <h6>SUSPEND</h6>
                                              <label :for="'suspend_fancy_' + fancy.id" class="button-toggle-wrap">
                                                <input :id="'suspend_fancy_' + fancy.id" class="toggler" type="checkbox" v-model="fancy.is_sus" @change="manageMarkets('Fancy', 'suspend', fancy)" />
                                                <div class="button-toggle danger">
                                                  <div class="handle">
                                                    <div class="bars"></div>
                                                  </div>
                                                </div>
                                              </label>
                                            </div>
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <h6>ACTIVE</h6>
                                              <label :for="'active_fancy_' + fancy.id" class="button-toggle-wrap">
                                                <input :id="'active_fancy_' + fancy.id" class="toggler" type="checkbox" v-model="fancy.is_active" @change="manageMarkets('Fancy', 'active', fancy)" />
                                                <div class="button-toggle">
                                                  <div class="handle">
                                                    <div class="bars"></div>
                                                  </div>
                                                </div>
                                              </label>
                                            </div>
                                            <div class="manage-dtl-block d-flex justify-content-between flex-row">
                                              <label class="button-toggle-wrap">
                                                <div class="chart-body-heading-two" style="padding-right: 0px">
                                                  <button href="javascript:void(0)" style="background: #4f687d" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default bet-btn" title="Bets"  @click="openBookOrBetModal(null, 'BF', fancy.runner_name, '', 'bets-fancy-bookmaker-market-modal', true, null, fancy.id)">Bets</button>
                                                </div>
                                              </label>
                                            </div>
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="match-odds-outer d-flex justify-content-between flex-row">
                                      <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                        <div class="match-odds-title-block">
                                          <span :data-tooltip="fancy.is_com ? 'Commission' : 'No Commission'" data-tooltip-location="right" :class="fancy.is_com ? 'badge border-success badge-pill info badge-border fancy-com-text' : 'badge border-warning badge-pill warning badge-border fancy-com-text'">{{
                                            fancy.is_com ? "CM" : "NC"
                                          }}</span>
                                          <h5 class="match-odds-title">
                                            {{ fancy.runner_name }}
                                          </h5>
                                          <div class="match-odds-tag danger">
                                            <span>{{ fancy.exposure}}</span>
                                          </div>
                                        </div>
                                      </a>
                                      <div class="price-odds-items-block d-flex justify-content-between flex-row">
                                        <div v-if="fancy.is_sus" class="d-flex justify-content-between flex-row mr-8 suspended suspended-small pos-rel" data-content="suspended">
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title"></h5>
                                            <div class="price-odd"></div>
                                          </a>
                                        </div>
                                        <div v-else class="d-flex justify-content-between flex-row mr-8 pos-rel">
                                          <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.yes_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ (fancy.yes_odd != "" && fancy.yes_odd != null ? (fancy.max_stake != null && fancy.max_stake != "" ? fancy.max_stake : currentSport.stake_size.max_fancy) : "") | kiloConverter }}
                                            </div>
                                          </a>
                                          <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                            <h5 class="price-odd-title">
                                              {{ fancy.no_odd }}
                                            </h5>
                                            <div class="price-odd">
                                              {{ (fancy.no_odd != "" && fancy.no_odd != null ? (fancy.max_stake != null && fancy.max_stake != "" ? fancy.max_stake : currentSport.stake_size.max_fancy) : "") | kiloConverter }}
                                            </div>
                                          </a>
                                        </div>
                                        <div class="price-odds-min-max-block text-right min-max-text">
                                          <div class="price-odds-min-max-title">
                                            Min:
                                            {{ fancy.min_stake != null && fancy.min_stake != "" ? fancy.min_stake : currentSport.stake_size.min_fancy }}
                                          </div>
                                          <div class="price-odds-min-max-title">
                                            Max:
                                            {{ (fancy.max_stake != null && fancy.max_stake != "" ? fancy.max_stake : currentSport.stake_size.max_fancy) | kiloConverter }}
                                          </div>
                                        </div>
                                      </div>
                                    </div>
                                    <div class="fancy-msg-block" v-if="fancy.msg != null && fancy.msg != ''">
                                      <div class="fancy-msg">
                                        <marquee>{{ fancy.msg }}</marquee>
                                      </div>
                                    </div>
                                  </div>
                                </li>
                              </ul>
                            </div>
                          </div>
                        </div>
                      </div>
                    </div>
                    <div class="tab-pane fade" id="other" role="tabpanel" aria-labelledby="other-tab">
                      <!-- Other Odds -->
                      <div class="chart-body-outer" v-if="market.is_active && market.is_declared == 0 && market.market_name != 'Match Odds'" v-for="(market, mIndex) in markets">
                        <div class="chart-body-heading-block">
                          <div class="chart-body-heading">
                            <div class="d-flex justify-content-between flex-row">
                              <h6 class="chart-body-heading chart-body-main-heading pt-1">
                                <span>{{ market.market_name }}</span>
                              </h6>
                              <div class="chart-body-heading-details">
                                <div class="d-flex flex-row justify-content-between">
                                  <div class="chart-body-heading chart-body-heading-outer d-flex flex-row justify-content-end" style="line-height: 29px">
                                    <div class="chart-body-heading-two">
                                      <button href="javascript:void(0)" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default book-btn" title="Booking"  @click="openBookOrBetModal(market.id, 'M', market.market_name, '', 'book-bookmaker-market-modal', true)">Book</button>
                                    </div>
                                    <div class="chart-body-heading-two">
                                      <button href="javascript:void(0)" style="background: #4f687d" onmouseover="this.style.color='#ffffff'" class="btn btn-sm btn-default bet-btn" title="Bets"  @click="openBookOrBetModal(market.id, 'BM', market.market_name, '', 'bets-fancy-bookmaker-market-modal', true, null, null)">Bets</button>
                                    </div>
                                  </div>
                                </div>
                              </div>
                            </div>
                          </div>
                        </div>

                        <div class="chart-body-heading-block" style="margin-top: 10px">
                          <div class="chart-body-heading">
                            <div class="d-flex justify-content-between flex-row" style="justify-content: flex-end !important">
                              <h6 class="chart-body-heading chart-body-main-heading" style="display: none">
                                <span>{{ market.market_name }}</span>
                              </h6>
                              <div class="chart-body-heading-details">
                                <div class="d-flex flex-row justify-content-between">
                                  <div class="chart-body-heading chart-body-heading-outer d-flex flex-row justify-content-end">
                                    <div class="chart-body-heading-two">
                                      <span>Back</span>
                                    </div>
                                  </div>
                                  <div class="chart-body-heading chart-body-heading-outer-two chart-body-heading-outer d-flex flex-row">
                                    <div class="chart-body-heading-one">
                                      <span>Lay</span>
                                    </div>
                                  </div>
                                </div>
                              </div>
                            </div>
                          </div>
                        </div>
                        <div :class="event.is_sus || event.is_active == 0 || market.is_sus || market.data == null ? 'chart-body suspended' : 'chart-body'" data-content="suspended">
                          <ul v-if="event.is_sus || event.is_active == 0 || market.is_sus || market.data == null">
                            <li v-for="(runner, rIndex) in market.selection_runner">
                              <div class="match-odds-outer d-flex justify-content-between flex-row" >
                                <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                  <div class="match-odds-title-block">
                                    <h5 class="match-odds-title">
                                      {{ runner.runner_name }}
                                    </h5>
                                    <div v-for="(m_runner_books, runner_bookIndex) in market.runner_books" v-if="m_runner_books.selection_id == runner.selection_id" :class="m_runner_books.pl > 0 ? 'match-odds-tag danger' : m_runner_books.pl < 0 ? 'match-odds-tag success' : 'match-odds-tag'">
                                      <span>{{ m_runner_books.pl | positive | addCommas }}</span>
                                    </div>
                                  </div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                  <h5 class="price-odd-title"></h5>
                                  <div class="price-odd"></div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                  <h5 class="price-odd-title"></h5>
                                  <div class="price-odd"></div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                  <h5 class="price-odd-title"></h5>
                                  <div class="price-odd"></div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                  <h5 class="price-odd-title"></h5>
                                  <div class="price-odd"></div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                  <h5 class="price-odd-title"></h5>
                                  <div class="price-odd"></div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                  <h5 class="price-odd-title"></h5>
                                  <div class="price-odd"></div>
                                </a>
                              </div>
                            </li>
                            <li v-if="currentSport.stake_size != null">
                              <div class="match-odds-outer d-flex justify-content-end flex-row min-max-text" style="margin-right: 3px; margin-top: 5px">
                                Min:
                                {{ currentSport.stake_size.min_match }} |&nbsp;
                                <span v-if="event.is_custom != 1">Max: {{ event.inplay ? (market.inplay_stake_limit != null ? market.inplay_stake_limit : (event.match_setting != null && event.match_setting.is_active && (event.match_setting.exch_stake_limit != null && event.match_setting.exch_stake_limit != '') ? event.match_setting.exch_stake_limit : (market.stake_limit != null && market.stake_limit != '' ? market.stake_limit :  currentSport.stake_size.max_match))) : (event.match_setting != null && event.match_setting.is_active && (event.match_setting.exch_stake_limit != null && event.match_setting.exch_stake_limit != '') ? event.match_setting.exch_stake_limit : (market.stake_limit != null && market.stake_limit != '' ? market.stake_limit :  currentSport.stake_size.max_match)) | kiloConverter }}</span>
                                <span v-if="event.is_custom == 1"> Max: {{ event.match_setting != null && event.match_setting.is_active && (event.match_setting.exch_stake_limit != null && event.match_setting.exch_stake_limit != '') ? event.match_setting.exch_stake_limit : (market.stake_limit != null && market.stake_limit != '' ? market.stake_limit :  currentSport.stake_size.max_match) | kiloConverter }}</span>
                              </div>
                            </li>
                          </ul>
                          <ul v-else>
                            <li v-for="(runner, rIndex) in market.selection_runner">
                              <div class="match-odds-outer d-flex justify-content-between flex-row" v-for="(data_runner, data_runner_Index) in market.data.runners" v-if="data_runner.selectionId == runner.selection_id">
                                <a href="javascript:void(0)" class="flex-grow-1" title="price">
                                  <div class="match-odds-title-block">
                                    <h5 class="match-odds-title">
                                      {{ runner.runner_name }}
                                    </h5>
                                    <div v-for="(m_runner_books, runner_bookIndex) in market.runner_books" v-if="m_runner_books.selection_id == runner.selection_id" :class="m_runner_books.pl > 0 ? 'match-odds-tag danger' : m_runner_books.pl < 0 ? 'match-odds-tag success' : 'match-odds-tag'">
                                      <span>{{ m_runner_books.pl | positive | addCommas }}</span>
                                    </div>
                                  </div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                  <h5 class="price-odd-title">
                                    {{ isset(data_runner.ex.availableToBack[2]) ? data_runner.ex.availableToBack[2].price : "" }}
                                  </h5>
                                  <div class="price-odd">
                                    {{ isset(data_runner.ex.availableToBack[2]) ? data_runner.ex.availableToBack[2].size : "" }}
                                  </div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block lightgreen hide-on-small" title="price">
                                  <h5 class="price-odd-title">
                                    {{ isset(data_runner.ex.availableToBack[1]) ? data_runner.ex.availableToBack[1].price : "" }}
                                  </h5>
                                  <div class="price-odd">
                                    {{ isset(data_runner.ex.availableToBack[1]) ? data_runner.ex.availableToBack[1].size : "" }}
                                  </div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block lightblue" title="price">
                                  <h5 class="price-odd-title">
                                    {{ isset(data_runner.ex.availableToBack[0]) ? data_runner.ex.availableToBack[0].price : "" }}
                                  </h5>
                                  <div class="price-odd">
                                    {{ isset(data_runner.ex.availableToBack[0]) ? data_runner.ex.availableToBack[0].size : "" }}
                                  </div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block purple" title="price">
                                  <h5 class="price-odd-title">
                                    {{ isset(data_runner.ex.availableToLay[0]) ? data_runner.ex.availableToLay[0].price : "" }}
                                  </h5>
                                  <div class="price-odd">
                                    {{ isset(data_runner.ex.availableToLay[0]) ? data_runner.ex.availableToLay[0].size : "" }}
                                  </div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                  <h5 class="price-odd-title">
                                    {{ isset(data_runner.ex.availableToLay[1]) ? data_runner.ex.availableToLay[1].price : "" }}
                                  </h5>
                                  <div class="price-odd">
                                    {{ isset(data_runner.ex.availableToLay[1]) ? data_runner.ex.availableToLay[1].size : "" }}
                                  </div>
                                </a>
                                <a href="javascript:void(0)" class="price-odds-block pink hide-on-small" title="price">
                                  <h5 class="price-odd-title">
                                    {{ isset(data_runner.ex.availableToLay[2]) ? data_runner.ex.availableToLay[2].price : "" }}
                                  </h5>
                                  <div class="price-odd">
                                    {{ isset(data_runner.ex.availableToLay[2]) ? data_runner.ex.availableToLay[2].size : "" }}
                                  </div>
                                </a>
                              </div>
                            </li>
                            <li v-if="currentSport.stake_size != null">
                              <div class="match-odds-outer d-flex justify-content-end flex-row min-max-text" style="margin-right: 3px; margin-top: 5px">
                                Min:
                                {{ currentSport.stake_size.min_match }} |&nbsp;
                                <span v-if="event.is_custom != 1">Max: {{ event.inplay ? (market.inplay_stake_limit != null ? market.inplay_stake_limit : (event.match_setting != null && event.match_setting.is_active && (event.match_setting.exch_stake_limit != null && event.match_setting.exch_stake_limit != '') ? event.match_setting.exch_stake_limit : (market.stake_limit != null && market.stake_limit != '' ? market.stake_limit :  currentSport.stake_size.max_match))) : (event.match_setting != null && event.match_setting.is_active && (event.match_setting.exch_stake_limit != null && event.match_setting.exch_stake_limit != '') ? event.match_setting.exch_stake_limit : (market.stake_limit != null && market.stake_limit != '' ? market.stake_limit :  currentSport.stake_size.max_match)) | kiloConverter }}</span>
                                <span v-if="event.is_custom == 1"> Max: {{ event.match_setting != null && event.match_setting.is_active && (event.match_setting.exch_stake_limit != null && event.match_setting.exch_stake_limit != '') ? event.match_setting.exch_stake_limit : (market.stake_limit != null && market.stake_limit != '' ? market.stake_limit :  currentSport.stake_size.max_match) | kiloConverter }}</span>
                              </div>
                            </li>
                          </ul>
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="event-new-test content-body-sidebar content-body-sidebar-my-market content-body-sidebar-no-scroll">
        <div class="content-body-sidebar-block live-score-desktop">
          <div class="panel with-nav-tabs panel-default">
            <div class="panel-heading">
              <div class="section-heading-block">
                <ul class="nav nav-tabs" style="border: none">
                  <li class="active live-score-board-tab">
                    <a href="#tab1default" @click="collapsePanel('scorecard')" data-toggle="tab"><h5 class="section-heading">Live Score</h5></a>
                  </li>
                  <li class="live-score-board-tab" style="margin-left: 15px">
                    <a href="#tab2default" @click="collapsePanel('tv')" data-toggle="tab"><h5 class="section-heading">Live TV</h5></a>
                  </li>
                  <li style="margin-left: auto; font-size: 22px">
                    <i class="fa fa-angle-down" @click="collapsePanel('scorecard')" data-toggle="collapse" aria-expanded="false" aria-controls="panel-score-collapse" data-target="#panel-score-collapse" aria-hidden="true"></i>
                  </li>
                </ul>
              </div>
            </div>
            <div class="panel-body collapse-in show" id="panel-score-collapse">
              <div class="tab-content">
                <div class="tab-pane fade in active show" id="tab1default" v-if="tab_opened == 'scorecard'">
                  <div class="live-score-content" style="padding: 0; margin: 5px">
                    <div class="widgets" v-if="event != null">
                        <div v-if="logo == 'bullexch' || logo == 'betdaily' || logo == 'mmo' || logo == 'hdexch444'"><div class="sr-widget sr-widget-2"></div></div>
                    </div>
                  </div>
                </div>
                <div class="tab-pane fade" id="tab2default" v-if="tab_opened == 'tv'">
                    <div v-if="auth._role != '' && !mobileDevice && !event.is_custom">
                        <iframe :src='"https://vid.dreamcasino.live/GetAPI.html?MatchID=" + event.event_id' scrolling="no" frameborder="0" :style="mobileDevice ? 'height: 230px !important; width: 100%;' : 'height: 235px !important; width: 100%;'"></iframe>
                    </div>
                    <div v-if="auth._role != '' && !mobileDevice && event.is_custom">
                        <iframe :src='"https://vid.dreamcasino.live/GetAPI.html?MatchID=" + event.tv_url' scrolling="no" frameborder="0" :style="mobileDevice ? 'height: 230px !important; width: 100%;' : 'height: 235px !important; width: 100%;'"></iframe>
                    </div>
                </div>
                <div class="tab-pane fade" id="tab3defaultDesktop">
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="content-body-sidebar-block">
          <div class="section-heading-block d-flex justify-content-between flex-row">
            <h5 class="section-heading">
              MATCH BETS &nbsp;
              <span class="text-info" v-if="matchBetsPagination != null">{{ matchBetsPagination.total }}</span>
            </h5>
            <a onmouseover="this.style.color='#ffffff'" href="javascript:void(0)"  @click="openBookOrBetModal(null, 'MatchBets', 'Match Bets', '', 'bets-fancy-bookmaker-market-modal', true, null)"><i class="material-icons" style="color: red; margin: 4px 0px">visibility</i></a>
          </div>
          <div data-simplebar class="sidebar-info-block-outer pad-0" style="max-height: 200px; overflow-x: auto; overflow-y: auto">
            <table class="table">
              <thead class="thead">
                <tr>
                  <th class="text-left">USER</th>
                  <th class="text-left">SELECTION</th>
                  <th class="text-left">ODDS</th>
                  <th class="text-left">STAKE</th>
                  <th class="text-left">DATE</th>
                  <th class="text-left">PARENT</th>
                  <th class="text-left">IP</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(bet, index) in matchBets" :key="index">
                  <th class="text-left pos-rel">
                    <span>{{ bet.bet_user_id != null ? bet.bet_user_id : bet.userid }}</span>
                  </th>
                  <th class="text-left">
                    {{ bet.selection }}
                    <span class="badge badge-primary badge-light" v-if="bet.selection_type == 'back'">BACK</span>
                    <span class="badge badge-danger badge-light" v-if="bet.selection_type == 'lay'">LAY</span>
                  </th>
                  <th class="text-left text-primary">{{ bet.odds }}</th>
                  <th class="text-left">
                    {{ bet.stake | positive | addCommas }}
                  </th>
                  <th class="text-left">{{ timestampConverterForFormat(bet.bet_timestamp_date, 'MMM D h:mm A') }}</th>
                  <th class="text-left">{{ bet.bet_parent_user_id }}</th>
                  <th class="text-left text-primary">{{ bet.ip }}</th>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
        <div class="content-body-sidebar-block">
          <div class="section-heading-block d-flex justify-content-between flex-row">
            <h5 class="section-heading">
              FANCY BETS &nbsp;
              <span class="text-info" v-if="fancyBetsPagination != null"> {{ fancyBetsPagination.total }}</span>
            </h5>
            <a onmouseover="this.style.color='#ffffff'"  href="javascript:void(0)"  @click="openBookOrBetModal(null, 'FancyBets', 'Fancy Bets', '', 'bets-fancy-bookmaker-market-modal', true, null)"><i class="material-icons" style="color: red; margin: 4px 0px">visibility</i></a>
          </div>
          <div data-simplebar class="sidebar-info-block-outer pad-0" style="max-height: 200px; overflow-x: auto; overflow-y: auto">
            <table class="table">
              <thead class="thead">
                <tr>
                  <th class="text-left">USER</th>
                  <th class="text-left">SELECTION</th>
                  <th class="text-left">ODDS</th>
                  <th class="text-left">RATE</th>
                  <th class="text-left">STAKE</th>
                  <th class="text-left">DATE</th>
                  <th class="text-left">PARENT</th>
                  <th class="text-left">IP</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(fBet, index) in fancyBets">
                  <th class="text-left pos-rel">
                    <span>{{ fBet.bet_user_id != null ? fBet.bet_user_id : fBet.userid }}</span>
                  </th>
                  <th class="text-left">
                    {{ fBet.selection }}
                    <span class="badge badge-primary badge-light" v-if="fBet.selection_type == 'back'">BACK</span>
                    <span class="badge badge-danger badge-light" v-if="fBet.selection_type == 'lay'">LAY</span>
                  </th>
                  <th class="text-left text-primary">{{ fBet.odds }}</th>
                  <th class="text-left text-primary">{{fBet.bet_market_type == 3 && fBet.fancy_rate != 0 ? fBet.fancy_rate : ''}}</th>
                  <th class="text-left">
                    {{ fBet.stake | positive | addCommas }}
                  </th>
                  <th class="text-left">{{ timestampConverterForFormat(fBet.bet_timestamp_date, 'MMM D h:mm A') }}</th>
                  <th class="text-left">{{ fBet.bet_parent_user_id }}</th>
                  <th class="text-left text-primary">{{ fBet.ip }}</th>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>
    <!-- Book Modal -->
    <modal name="book-modal" transition="nice-modal-fade" :clickToClose="false" :scrollable="true" :draggable="false" :adaptive="true"
        :resizable="false" id="fancy_book_modal" class="modalSize modales" :min-width="100" :max-width="300" :max-height="1000" style="background: transparent !important;">
      <div>
        <div class="modal-header">
          <h4 class="modal-title">
            TOTAL BETS
            <span class="badge badge-primary" v-if="fancyBook.length > 0">{{ fancyBook.length }}</span>
          </h4>
          <h4 style="top: 2px; position: relative">
            <span class="badge badge-warning" style="font-family: 'FontAwesome'; font-weight: normal; font-style: normal; font-size: 14px; letter-spacing: normal; text-transform: none; display: inline-block; white-space: nowrap">FANCY</span>
          </h4>
          <a href="javascript:void(0)" class="default-close-btn" title="Close" @click="$modal.hide('book-modal')"><i class="material-icons">close</i></a>
        </div>
        <div class="modal-body">
          <div v-if="!open_modal">
            <spinner style="left: 38% !important"></spinner>
          </div>
          <div class="card-body" style="height: 100%; padding-left: 0; padding-right: 0; margin-bottom: 50px !important;" v-else>
            <div v-if="fancyBook.length > 0">
              <div class="vue-modal-sub-header">
                <div class="row">
                  <div class="col-6 text-center">
                    <h4>Position</h4>
                  </div>
                  <div class="col-6 text-center">
                    <h4>Profit / Loss</h4>
                  </div>
                </div>
              </div>
              <div class="vue-modal-scroll">
                <div class="row">
                  <div class="col-6 text-center pad-0">
                    <h5 v-if="fancyBook.length > 0" v-for="(book, index) in fancyBook" :class="book.result > 0 ? 'bg-danger-light pos-rel' : book.result < 0 ? 'bg-success-light pos-rel' : ''">
                      {{ book.odds }}
                      <div class="book-arrow-block">
                        <i :class="book.result > 0 ? 'fa fa-long-arrow-right text-danger' : book.result < 0 ? 'fa fa-long-arrow-right text-success' : ''"></i>
                      </div>
                    </h5>
                  </div>
                  <div class="col-6 text-center pad-0">
                    <h5 v-if="fancyBook.length > 0" v-for="(book, index) in fancyBook" :class="book.result > 0 ? 'bg-danger-light' : book.result < 0 ? 'bg-success-light' : ''">
                      {{ book.result | positive }}
                    </h5>
                  </div>
                </div>
              </div>
            </div>
            <!-- <div v-else>
              <div style="padding-bottom: 30px">
                <h4 class="text-center" style="margin: 20px 0 5px">No bet placed yet !</h4>
              </div>
            </div>-->
          </div>
        </div>
      </div>
    </modal>
    <!-- Delete Modal -->
    <modal name="delete-modal" transition="nice-modal-fade" :clickToClose="false" :scrollable="true" :min-width="100" :max-width="300" height="auto" :draggable="false" :adaptive="true" style="z-index:3000">
      <div>
        <div class="modal-header">
          <h4 class="modal-title">DELETE BET ?</h4>
          <a href="javascript:void(0)" class="default-close-btn" title="Close" @click="$modal.hide('delete-modal')"><i class="material-icons">close</i></a>
        </div>
        <div class="modal-body" style="padding: 10px">
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
    <!-- Bets Show -->
    <!-- Bookmaker and Market Book Modal -->
    <modal name="book-bookmaker-market-modal" :clickToClose="false" :scrollable="true" :adaptive="true"
        id="book_bookmaker_market_modal" style="background: transparent !important; height: auto;">
        <div v-if="opened_modal != null">
            <div class="modal-header flex-row justify-content-between">
                <h4 class="modal-title" :style="mobileDevice ? 'width: 80px;': ''">
                    {{opened_modal.market_name}}
                    <span class="badge badge-primary">{{opened_modal.market_type}}</span>
                </h4>
                <input type="text" class="form-control form-control-sm" v-model="search_bookBets" placeholder="Search client">
                <!-- <button type="button" class="close modal-close default-close-btn" data-dismiss="modal" aria-label="Close" @click="closeBookOrBetModal('book-bookmaker-market-modal')">
                    <span aria-hidden="true">&times;</span>
                </button> -->
              <a data-v-81640eec="" href="javascript:void(0)" title="Close" class="default-close-btn" @click="closeBookOrBetModal('book-bookmaker-market-modal')"><i data-v-81640eec="" class="material-icons">close</i></a>
            </div>
            <div class="modal-body" v-if="opened_modal.is_loading">
                <spinner style="left: 38% !important"></spinner>
            </div>
            <div class="modal-body table-responsive" v-if="!opened_modal.is_loading && opened_modal.user_book != null">
                <table class="table">
                    <thead class="thead-dark">
                        <tr>
                            <th class="text-center">CLIENT</th>
                            <!--bookmaker or main market -->
                            <th class="text-center" v-if="opened_modal.bookmaker_or_market != null && opened_modal.bookmaker_or_market.selection_runner != null" v-for="runner in opened_modal.bookmaker_or_market.selection_runner">
                                {{ runner.runner_name }}
                            </th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(item, index) in filteredModalBookContent" :key="index">
                            <th class="text-center">
                                <a @click.prevent="getChildBooks(item)" v-if="item.is_parent" class="text-primary" href="javascript:void(0)">{{ item.user.toUpperCase() }}</a>
                                <div v-if="item.is_parent == false">
                                    {{ item.user.toUpperCase() }}
                                </div>
                            </th>
                            <td class="text-center" v-for="(runner_books, index1) in item.client_runner_books" :key="item+':'+index1">

                                <span :class="runner_books.pl < 0 ?'text-success bold' : 'text-danger bold'">
                                    {{runner_books.pl | positive | addCommas}}
                                </span>
                            </td>
                        </tr>
                    </tbody>
                    <tfoot v-if="opened_modal.total != null">
                        <tr>
                            <td class="text-center bold">TOTAL</td>
                            <td v-for="(total_item, index) in opened_modal.total" :key="index" class="text-center">
                                <span :class="total_item < 0 ?'text-success bold' : 'text-danger bold'">
                                    {{ total_item | positive | addCommas}}
                                </span>
                            </td>
                        </tr>
                    </tfoot>
                </table>
            </div>
        </div>
    </modal>
    <modal name="bets-fancy-bookmaker-market-modal" :clickToClose="false" :scrollable="true" :adaptive="true"
        id="bets_fancy_bookmaker_market_modal" style="background: transparent !important; height: auto;">
        <div v-if="opened_modal != null">
            <div class="modal-content">
              <div class="modal-header">
                <h4 class="modal-title" style="width: 169px;">
                  {{ opened_modal.market_name }}
                  <span class="text-info">{{ opened_modal.user_bet != null ? opened_modal.user_bet.total : 0 }}</span>
                </h4>
                <div class="form-group">
                    <label for="" style="font-size: 16px;">Auto Refresh</label>
                    <input for="zero_amount_hide" type="checkbox" v-model="auto_refresh_bets" />
                </div>
                <a href="javascript:void(0)" class="default-close-btn" title="Close" @click="closeBookOrBetModal('bets-fancy-bookmaker-market-modal')"><i class="material-icons">close</i></a>
              </div>
              <div class="modal-header">
                <div class="form-group" style="width: 30%;">
                    <label for="">Search By</label>
                    <select class="form-control" v-model="bets_search_by">
                        <option value="user">User</option>
                        <option value="selection">Selection</option>
                        <option value="stake">Stake</option>
                    </select>
                </div>
                <div class="form-group" style="width: 68%;">
                    <label for="">Search</label>
                    <div class="input-group">
                        <input type="text" class="form-control" v-model="search_bets_market" v-on:keyup.enter="filterBetsModal" placeholder="Search for...">
                        <span class="input-group-btn search-btn-style" @click="filterBetsModal">
                            <button class="btn btn-default" type="submit">
                                <i class="fa fa-search"></i>
                            </button>
                        </span>
                    </div>
                </div>
              </div>
              <div class="modal-header" v-if="auth._role == 'is_director' && selectedBetsID != null && selectedBetsID.length > 0">
                  <button class="btn btn-danger" style="width: 100%;" @click="multibetsdelets('')">Delete</button>
              </div>
              <div class="modal-body" style="padding: 10px; overflow-y: scroll">
                <div class="card-body" v-if="opened_modal.is_loading">
                  <spinner></spinner>
                </div>
                <div class="card-body" v-if="!opened_modal.is_loading && opened_modal.user_bet != null">
                    <div class="d-flex flex-row justify-content-between table-responsive">
                        <table class="table">
                        <thead class="thead">
                            <tr>
                            <th class="text-left">#</th>
                            <th class="text-left">
                                <input type="checkbox" v-if="auth._role == 'is_director'" v-model="allSelected" @click="selectAll"> USER</th>
                            <th class="text-left">SELECTION</th>
                            <th class="text-left">ODDS</th>
                            <th class="text-left">RATE</th>
                            <th class="text-left">STAKE</th>
                            <th class="text-left">DATE</th>
                            <th class="text-left">PARENT</th>
                            <th class="text-left">IP</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="(bet, index) in opened_modal.user_bet.data" :key="index">
                            <th class="text-left">{{ index+1 }}</th>
                            <th class="text-left pos-rel" v-if="auth._role == 'is_director'">
                                <input type="checkbox" v-model="selectedBetsID" :id="bet.id" :value="bet.id" class="multiBetsDelete-checked" />
                                <span style="margin-left: 22px">{{ bet.bet_user_id }}</span>
                            </th>
                            <th v-else class="text-left">{{ bet.bet_user_id }}</th>
                            <th class="text-left">
                                {{ bet.selection }}
                                <span class="badge badge-primary badge-light" v-if="bet.selection_type == 'back'">BACK</span>
                                <span class="badge badge-danger badge-light" v-if="bet.selection_type == 'lay'">LAY</span>
                            </th>
                            <th class="text-left text-primary">{{ bet.odds }}</th>
                            <th class="text-left text-primary">{{bet.bet_market_type == 3 && bet.fancy_rate != 0 ? bet.fancy_rate : ''}}</th>
                            <th class="text-left">
                                {{ bet.stake | positive | addCommas }}
                            </th>
                            <th class="text-left">{{ timestampConverterForFormat(bet.bet_timestamp_date, 'MMM D h:mm A') }}</th>
                            <th class="text-left">{{ bet.bet_parent_user_id }}</th>
                            <th class="text-left text-primary">{{ bet.ip }}</th>
                            </tr>
                        </tbody>
                        </table>
                    </div>
                    <br>
                    <div class="d-flex flex-lg-row flex-column justify-content-center table-bottom-footer m-0" style="margin-bottom: 100px !important;">
                        <div class="mb-1 mr-updated-5 text-center" v-if="opened_modal.user_bet != null">
                            <div>
                                <input type="number" class="form-control custom-form-control-right" title="Enter page number" placeholder="Page No." v-model="select_page_markets_bets" :disabled="true" />
                            </div>
                        </div>
                        <div class="mb-1 text-center">
                            <div class="d-flex flex-row justify-content-center" v-if="opened_modal.user_bet != null" v-on="callEventHandlerBets(opened_modal)">
                                <paginate v-model="selected_page_bets"
                                :page-count="opened_modal.user_bet.last_page != null ? opened_modal.user_bet.last_page : 0"
                                :page-range="5"
                                :margin-pages="5"
                                :click-handler="betsModelOpen"
                                :prev-text="'Prev'"
                                :next-text="'Next'"
                                :container-class="'pagination'"
                                :page-class="'page-item'">
                                </paginate>
                            </div>
                        </div>
                        <div class="mb-1 ml-lg-5 text-center">
                            <div>
                                <label class="font-medium">Show </label>
                                <select class="form-control custom-form-control-left" title="Select entries for individual page." v-model="select_entries_bets" @change="selectEntriesBets">
                                <option value="200">200</option>
                                <option value="350">350</option>
                                <option value="500">500</option>
                                </select>
                                <label class="font-medium"> Entries</label>
                            </div>
                        </div>
                    </div>
                </div>
              </div>
            </div>
        </div>
    </modal>
  </div>
</template>
<script>
import { mapState } from "vuex";
import Spinner from "../Assets/Spinner";
export default {
  props: ["event_slug", 'tvurl', 'eventSport', 'matchIdProp'],
  data() {
    return {
      open_modal: false,
      current_user: "",
      is_loading: false,
      event: null,
      markets: [],
      bookmakersBooks: [],
      normal_fancies: null,
      khado_fancies: null,
      meter_fancies: null,
      ball_by_ball_fancies: null,
      fancy_1_fancies: null,
      fancyBook: [],
      matchBets: [],
      fancyBets: [],
      clients: [],
      matchBetsPagination: null,
      fancyBetsPagination: null,
      init: true,
      is_destroy: false,
      fancyExposureArr: [],
      getMatchIdFancy: null,
      select_page: 0,
      select_entries: 200,
      selected_page: 0,
      select_page_fancy: 0,
      selected_page_bets : 0, // markets Model
      select_page_markets_bets: 0,
      select_entries_bets: 200,
      betsEventhandler: { marketModel: null },
      auth_user_id: "",
      tab_opened: 'scorecard',
      selected: [],
      allSelected: false,
      selectedBetsID: [],
      opened_modal: null,
      search_bookBets: '',
      search_bets_market: '',
      bets_search_by: 'user',
      auto_refresh_bets: false,
      logo: '',
      bet_delete_status:'',
    };
  },
  components: {
    sinner: Spinner,
  },
  watch: {
    selectedBetsID(value) {
      if(this.opened_modal != null && this.opened_modal.user_bet != null && this.opened_modal.user_bet.data.length == value.length) {
        this.allSelected = true;
      } else {
        this.allSelected = false;
      }
    },
    auto_refresh_bets(value) {
      if(value) {
        this.betsModelOpen(this.select_page_markets_bets, true);
      }
    }
  },
  computed: {
    currentSport() {
      var c_sport = {
        name: "",
      };
      if (this.event != null) {
        c_sport = this.event.sport;
      }
      return c_sport;
    },
    ...mapState(["auth", "origin_path", "all_sports", "mobileDevice", "current_bookmakers", "host_origin"]),
    filteredModalBookContent() {
        if(this.opened_modal != null && this.opened_modal.user_book != null && this.search_bookBets != '') {
            return this.opened_modal.user_book.filter(item => {
                return item.user.toLowerCase().indexOf(this.search_bookBets.toLowerCase()) > -1
            })
        }
        return this.opened_modal != null && this.opened_modal.user_book != null ? this.opened_modal.user_book : [];
    }
  },
  filters: {
    kiloConverter: function (value) {
      var digit = parseInt(value) / 1000;
      return digit.toString() + "K";
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
      if (val != "" && val != null) {
        var val = parseFloat(val).toFixed(2);
        val += "";
        var x = val.split(".");
        var x1 = x[0];
        var x2 = x.length > 1 ? "." + x[1] : "";
        var rgx = /(\d+)(\d{3})/;
        while (rgx.test(x1)) {
          x1 = x1.replace(rgx, "$1" + "," + "$2");
        }
        total = x1 + x2;
      }
      return total;
    },
  },
  mounted() {
    if ($(window).width() < 1099) {
      setTimeout(function () {
        $(".event-detail-page-block").removeClass("flex-row");
        $(".event-detail-page-block").addClass("flex-column");
        $(".live-score-desktop").hide();
      }, 1600);
    }
  },
  beforeMount() {
    $("body").css({ position: "sticky"});
  },
  async created() {
    Echo.disconnect();
    Echo.connect();
    this.is_loading = true;
    $("body").removeClass("show-less-prices-active");
    $("body").removeClass("event-page");
    await this.getEvent();
    this.$store.commit("addCurrentBookmakers", []);
    if (host_origin != 'bullexch' && host_origin != 'mmo') {
        this.logo = host_origin;
    } else {
        this.logo = 'bullexch';
    }
  },
  beforeDestroy() {
    this.is_destroy = true;
  },
  methods: {
    filterBetsModal: function() {
      this.opened_modal.is_loading = true;
      this.selected_page_bets = 1;
      this.select_page_markets_bets = 1;
      this.betsModelOpen(1);
    },
    selectAll: function() {
      this.selectedBetsID = [];
      this.allSelected = this.allSelected ? false : true;
      if (this.allSelected && this.opened_modal != null && this.opened_modal.user_bet != null) {
        this.opened_modal.user_bet.data.forEach((bet_data) => {
          this.selectedBetsID.push(bet_data.id.toString());
        })
      }
    },
    callEventHandlerBets(market) {
      this.betsEventhandler = { marketModel: market};
    },
    selectEntriesBets() {
      this.selected_page_bets = 1;
      this.select_page_markets_bets = 1;
      this.betsModelOpen(this.select_page_markets_bets);
    },
    async betsModelOpen(pageNum, is_loading = true) {
      if (this.opened_modal.type == "BM" || this.opened_modal.type == "BB" || this.opened_modal.type == "BF" || this.opened_modal.type == "MatchBets" || this.opened_modal.type == "FancyBets") {
        await this.betsPaginateModel(this.opened_modal , pageNum, is_loading); // user All Bets Markets Wise(2)
      }
    },
    async betsPaginateModel(opened_modal, pageNum, is_loading = true) {
        this.opened_modal.is_loading = is_loading;
        var auth_user_id = await this.getCurrentAuth(); // current userId find
        if (pageNum == undefined) {
            pageNum = 1;
        }
        this.select_page_markets_bets = pageNum;
        var url = "/get-match-markets-bets";
        url += "?page=" + this.select_page_markets_bets;
        await axios
          .post(url, {
            match_id: this.event.id,
            market_id: opened_modal.marketId,
            bookmaker_id: opened_modal.bookmaker_id,
            fancy_id: opened_modal.fancy_id,
            user_id: auth_user_id,
            market_type: opened_modal.type,
            page_entries: this.select_entries_bets,
            search_string: this.search_bets_market,
            search_by: this.bets_search_by
          })
          .then((res) => {
              if (res.data.errorStatus) {
                this.$toasted.error(res.data.message, { icon: "not_interested" });
              } else {
                this.opened_modal.user_bet = res.data.user_bet;
                this.opened_modal.is_loading = false;
              }
              return;
          }).catch((e) => {
            this.opened_modal.is_loading = false;
            this.$toasted.error('Something went wrong !', { icon: "not_interested" });
          });
    },
    async getCurrentAuth() {
      this.auth_user_id = this.auth._role == "is_manager" ? 1 : this.auth.id;
      return this.auth_user_id;
    },
    multibetsdelets(type) {
        if(this.opened_modal.marketId != '' && (this.opened_modal.fancy_id == null || this.opened_modal.fancy_id == '')) {
            type = 'match-bets';
        } else if(this.opened_modal.fancy_id != '' && (this.opened_modal.marketId == null || this.opened_modal.marketId == '')
          && (this.opened_modal.bookmaker_id == null || this.opened_modal.bookmaker_id == '')) {
            type = 'fancy-bets';
        }
        this.$modal.show("delete-modal");
    },
    async getAllMarketsFancys() {
      await axios
        .get("/get-fancy-exposure/" + this.event.id)
        .then((res) => {
          this.getMatchIdFancy = res.data.exposure;
          this.getFancysExploser()
        })
        .catch((e) => {
          console.log(e);
        });
    },
    getFancysExploser() {
      this.fancyExposureArr = [];
      if(this.getMatchIdFancy != null && this.getMatchIdFancy.length > 0) {
        this.getMatchIdFancy.forEach((find, mindex) => {
          if(find.fancy_id != null) {
            this.fancyExposureArr[find.fancy_id] = find.fancyExposure;
          }
        })
      }
    },
    collapsePanel(type) {
      this.tab_opened = type;
      if(type === 'scorecard') {
        this.getEvent()
      }
      $("panel-score-collapse").toggleClass("collapse-in");
    },
    openBookOrBetModal(marketId = null , type = null, market_name, market_type, modal_name, is_loading = false, bookmaker_id = null, fancy_id = null) {
        this.opened_modal = {
            marketId: marketId,
            bookmaker_id: bookmaker_id,
            fancy_id: fancy_id,
            type: type,
            market_name: market_name,
            market_type: market_type,
            modal_name: modal_name,
            is_loading: is_loading,
            user_book: null,
            bookmaker_or_market: null,
            total: null,
            fancy_object: null,
        };
        var market_object = {
            match_id: this.event.id,
            market_id: marketId,
            bookmaker_id: bookmaker_id,
            user_id: this.auth.id,
            market_type: type,
            fancy_id: fancy_id,
            page_entries: null
        };
        this.$modal.show(modal_name);
        if(type == 'B') {
            var bookmaker = this.current_bookmakers.find((book) => book.id === bookmaker_id);
            this.opened_modal.bookmaker_or_market = bookmaker;
            this.callBookApi(market_object);
        } else if(type == 'M') {
            var mainMarket = this.markets.find((m) => m.id === marketId);
            this.opened_modal.bookmaker_or_market = mainMarket;
            this.callBookApi(market_object);
        } else if(type == 'BM' || type == "BB" || type == "BF" || type == 'FancyBets' || type == 'MatchBets') {
            if (bookmaker_id != null && this.current_bookmakers != null) {
                var bookmaker = this.current_bookmakers.find((book) => book.id === bookmaker_id);
                this.opened_modal.bookmaker_or_market = bookmaker;
            }
            else if (marketId != null && this.markets != null) {
                var mainMarket = this.markets.find((m) => m.id === marketId);
                this.opened_modal.bookmaker_or_market = mainMarket;
            }
            else if (fancy_id != null && this.normal_fancies != null) {
                var normal_fancies = this.normal_fancies.data.find((fone) => fone.id === fancy_id);
                this.opened_modal.fancy_object = normal_fancies;
            }
            else if (fancy_id != null && this.ball_by_ball_fancies != null) {
                var ball_by_ball_fancies = this.ball_by_ball_fancies.data.find((ftwo) => ftwo.id === fancy_id);
                this.opened_modal.fancy_object = ball_by_ball_fancies;
            }
            else if (fancy_id != null && this.khado_fancies != null) {
                var khado_fancies = this.khado_fancies.data.find((fthree) => fthree.id === fancy_id);
                this.opened_modal.fancy_object = khado_fancies;
            }
            else if (fancy_id != null && this.meter_fancies != null) {
                var meter_fancies = this.meter_fancies.data.find((four) => four.id === fancy_id);
                this.opened_modal.fancy_object = meter_fancies;
            }
            else if (fancy_id != null && this.fancy_1_fancies != null) {
                var fancy_1_fancies = this.fancy_1_fancies.data.find((five) => five.id === fancy_id);
                this.opened_modal.fancy_object = fancy_1_fancies;
            }
            this.callBetsApi(market_object)
        }
    },
    async callBookApi(book_object) {
        await axios.post("/get-markets-bet", book_object).then((res) => {
            if(!res.data.errorStatus) {
                this.opened_modal.user_book = res.data.user_book;
                this.opened_modal.is_loading = false;
                var total = []
                if(this.opened_modal.user_book != null && this.opened_modal.user_book.length > 0) {
                    this.opened_modal.user_book.forEach((userItem) => {
                            for(let b = 0; b < userItem.client_runner_books.length; b++) {
                                if(total[b] != null) {
                                    total[b] += userItem.client_runner_books[b].pl;
                                } else {
                                    total[b] = userItem.client_runner_books[b].pl;
                                }
                            }
                        });
                }
                this.opened_modal.total = total;
            } else {
                this.opened_modal.is_loading = false;
                this.$toasted.error('Something went wrong. please again later!', { icon: "not_interested"})
            }
        }).catch((e) => {
            this.opened_modal.is_loading = false;
            console.log(e.response);
        });
    },
    closeBookOrBetModal(modal_name) {
        this.opened_modal = null;
        this.selectedBetsID = [];
        this.auto_refresh_bets = false;
        this.getAllMarketsFancys();
        this.getParentMarketBooks();
        this.showMyBooksAndBets();
        this.$modal.hide(modal_name);
    },
    async callBetsApi(market_object){
        this.select_page_markets_bets = 1;
        let url = "/get-match-markets-bets";
        url += "?page=" + this.select_page_markets_bets;
        market_object.page_entries = this.select_entries_bets;
        await axios.post(url, market_object).then((res) => {
            this.opened_modal.user_bet = res.data.user_bet;
            this.opened_modal.is_loading = false;
        }).catch((e) => {
            this.opened_modal.is_loading = false;
            this.$toasted.error('Something went wrong!');
        });
    },
    deleteBet(type) {
        this.bet_delete_status  =   type;
        this.$modal.show("delete-bet-reason-modal");
    },
    deleteBetWithReason(reason) {
        this.$modal.hide("delete-bet-reason-modal");
        this.$modal.hide("delete-modal");
        if (this.selectedBetsID != null && this.selectedBetsID.length > 0) {
            axios.post("/delete-bet", { id: null, idsMulti: this.selectedBetsID, type: this.bet_delete_status.toString(), 'reason':reason}).then((res) => {
                if (res.data.error != null) {
                    this.$toasted.error(res.data.error, { icon: "not_interested" });
                } else {
                    this.selectedBetsID = [];
                    this.selected_page_bets = 1;
                    this.select_page_markets_bets = 1;
                    this.betsModelOpen(this.select_page_markets_bets);
                    this.$toasted.show("BET " + this.bet_delete_status.toString() + "ED" + " SUCCESSFULLY", {
                        icon: "check_circle",
                    });
                }
            }).catch((e) => {
                this.$toasted.error('Please try again..!', { icon: "not_interested" });
            });
        }
    },
    manageMarkets(market, type, market_object) {
        var market_id = '';
        var source = 'normal'; 
        if(market == 'Fancy') {
            if(market_object.sky_fancy_id != null) {
                market_id = market_object.sky_fancy_id;
                source = 'sky';
            } else {
                market_id = market_object.id;
                source = 'normal';
            }
        } 
        if(market == 'Bookmaker') {
            if(market_object.sky_bookmaker_id != null) {
                market_id = market_object.sky_bookmaker_id;
                source = 'sky';
            } else {
                market_id = market_object.id;
                source = 'normal';
            }
        }
        axios.post("/manage-market-in-event-details", {
            market: market,
            type: type,
            id: market_id,
            source: source
        }).then((res) => {
            if (res.data.error != null) {
                this.$toasted.error(res.data.error, { icon: "not_interested" });
            } else {
                this.$toasted.show("Market setting changed successfully", {icon: "check_circle"});
            }
        }).catch((e) => {
            this.$toasted.error('Please refresh page!', { icon: "not_interested" });
        });
    },
    total_book(rIndex) {
      var book = 0;
      for (var i = 0; i < this.clients.length; i++) {
        if (this.clients[i].client_runner_books != null) {
          book = parseInt(book) + parseInt(this.clients[i].client_runner_books[rIndex]);
        }
      }
      return book;
    },
    sortedArrayOfFancy(data) {
      function compare(a, b) {
        if (a.runner_name < b.runner_name) return -1;
        if (a.runner_name > b.runner_name) return 1;
        return 0;
      }
      return data.sort(compare);
    },
    isset(val) {
      if (typeof val === "undefined") {
        return false;
      } else {
        return true;
      }
    },
    async getEvent() {
        if(this.$router.history.current.path.includes('/my-market/')) {
            await axios.get("/get-event-by-slug/" + this.$props.event_slug).then((res) => {
                if (res.data.event == null) {
                    window.location.href = window.location.origin + "/my-market";
                } else {
                    this.event = res.data.event;
                    this.getEventMarkets();
                    var lmt = "match.lmtPlus";
                    if (this.$props.sport_slug == "cricket") {
                        lmt = "match.lmtlight";
                    }
                    var eventId = this.event.event_id;
                    var eventSportRadarId = this.event.sport_radar_id;
                    var sportradarMatchId = '';
                    if(eventSportRadarId != null && eventSportRadarId != '') {
                        sportradarMatchId = eventSportRadarId.toString();
                    } else {
                        sportradarMatchId = "m" + eventId.toString();
                    }
                    var mobileDevice = this.mobileDevice;
                    setTimeout(function () {
                    (function(a,b,c,d,e,f,g,h,i){a[e]||(i=a[e]=function(){(a[e].q=a[e].q||[]).push(arguments)},i.l=1*new Date,i.o=f,
                    g=b.createElement(c),h=b.getElementsByTagName(c)[0],g.async=1,g.src=d,g.setAttribute("n",e),h.parentNode.insertBefore(g,h)
                    )})(window,document,"script", "https://widgets.sir.sportradar.com/d4231fffdf2741dae2eceeb02b5fd67d/widgetloader", "SIR", {
                    theme: false, // using custom theme
                    language: "en"
                    });
                        if (mobileDevice) {
                            SIR("addWidget", ".sr-widget-1", lmt.toString(), {layout: "single", momentum: "disable", detailedScoreboard: "disable", matchId: sportradarMatchId});
                        } else {
                            SIR("addWidget", ".sr-widget-2", lmt.toString(), {layout: "single", momentum: "disable", detailedScoreboard: "disable", matchId: sportradarMatchId});
                        }
                    }, 1000);
                }
            }).catch((e) => {
                console.log(e.response);
            });
        }
    },
    async getEventMarkets() {
        await axios.get("/get-active-event-markets/" + this.$props.event_slug).then((res) => {
            if (res.data.markets.length == 0) {
                this.is_loading = false;
            }
            var data = res.data.markets;
            if (this.markets.length == res.data.markets.length) {
                for (var i = 0; i < data.length; i++) {
                    data[i].data = this.markets[i].data;
                    data[i].runner_books = this.markets[i].runner_books;
                    if (this.auth._role == "is_director") {
                        data[i].is_sus = this.markets[i].is_sus;
                        data[i].is_active = this.markets[i].is_active;
                        data[i].stake_limit = this.markets[i].stake_limit;
                    }
                }
            }
            this.markets = data;
            if (this.is_destroy == false) {
                if (this.init) {
                    this.getEventsMarketData();
                }
            }
            this.showMyBooksAndBets();
            this.getAllMarketsFancys();
            this.getParentMarketBooks();
        }).catch((e) => {
            this.$toasted.error('Please refresh page!', { icon: "not_interested" });
        });
    },
    async getEventsMarketData() {
        this.init = false;
        this.markets.forEach(
            function (market, index) {
                this.is_loading = false;
                this.init = false;
                Echo.channel("market." + market.market_id).listen("SportsBroadcastData", (data) => {
                    if (market.market_name == "Match Odds" && data.data != null) {
                    this.event.inplay = data.data.inplay;
                    }
                    this.markets[index].data = data.data;
                });
            }.bind(this)
        );
        await Echo.channel("fancy." + this.event.event_id).listen("BroadcastFancy", (data) => {
            if (data.data != null) {
                // Object To Array
                let array_1 = Object.keys(data.data.fancies.normal_fancies.data).map((key) => {
                    var object_fancy = data.data.fancies.normal_fancies.data[key];
                    object_fancy['exposure'] = this.fancyExposureArr[object_fancy['id']];
                    return object_fancy;
                });
                // Object To Array
                let array_2 = Object.keys(data.data.fancies.khado_fancies.data).map((key) => {
                    var object_fancy = data.data.fancies.khado_fancies.data[key];
                    object_fancy['exposure'] = this.fancyExposureArr[object_fancy['id']];
                    return object_fancy;
                });
                // Object To Array
                let array_3 = Object.keys(data.data.fancies.meter_fancies.data).map((key) => {
                    var object_fancy = data.data.fancies.meter_fancies.data[key];
                    object_fancy['exposure'] = this.fancyExposureArr[object_fancy['id']];
                    return object_fancy;
                });
                // Object To Array
                let array_4 = Object.keys(data.data.fancies.fancy_1_fancies.data).map((key) => {
                    var object_fancy = data.data.fancies.fancy_1_fancies.data[key];
                    object_fancy['exposure'] = this.fancyExposureArr[object_fancy['id']];
                    return object_fancy;
                });
                // Object To Array
                let array_5 = Object.keys(data.data.fancies.ball_by_ball_fancies.data).map((key) => {
                    var object_fancy = data.data.fancies.ball_by_ball_fancies.data[key];
                    object_fancy['exposure'] = this.fancyExposureArr[object_fancy['id']];
                    return object_fancy;
                });
                this.normal_fancies = data.data.fancies.normal_fancies;
                this.normal_fancies.data = this.sortedArrayOfFancy(array_1);

                this.khado_fancies = data.data.fancies.khado_fancies;
                this.khado_fancies.data = this.sortedArrayOfFancy(array_2);

                this.meter_fancies = data.data.fancies.meter_fancies;
                this.meter_fancies.data = this.sortedArrayOfFancy(array_3);

                this.fancy_1_fancies = data.data.fancies.fancy_1_fancies;
                this.fancy_1_fancies.data = this.sortedArrayOfFancy(array_4);

                this.ball_by_ball_fancies = data.data.fancies.ball_by_ball_fancies;
                this.ball_by_ball_fancies.data = this.sortedArrayOfFancy(array_5);
            } else {
                this.normal_fancies = null;
                this.khado_fancies = null;
                this.meter_fancies = null;
                this.fancy_1_fancies = null;
                this.ball_by_ball_fancies = null;
            }
        })
        await Echo.channel("bookmaker." + this.event.event_id).listen("BroadcastBookmaker", (data) => {
            var bookmakers = this.current_bookmakers;
            if (data.data != null) {
                bookmakers = data.data.bookmakers;
            } else {
                bookmakers = [];
            }
            bookmakers = Object.keys(bookmakers).map((i) => bookmakers[i]);
            this.$store.commit("addCurrentBookmakers", bookmakers);
        });

        await Echo.channel("refesh_my_market." + this.auth.id).listen("BroadcastBetPlacedEvent", (data) => {
            if(data.data != null && data.data.type == 'sports' && data.data.match_id == this.event.id) {
                if(this.opened_modal == null) {
                    // setTimeout(function() {
                    //     if(data.data.bet_type == 'Fancy' && this.fancyBets.find(x => x.id === data.data.item_id) == undefined) {
                    //         this.getAllMarketsFancys();
                    //         this.showMyBooksAndBets();
                    //     } else if(data.data.bet_type != 'Fancy' && this.matchBets.find(x => x.id === data.data.item_id) == undefined) {
                    //         this.getParentMarketBooks();
                    //         this.showMyBooksAndBets();
                    //     }
                    // }.bind(this), 3000);
                    if(data.data.bet_type == 'Fancy') {
                        this.updateFancyExposure(data.data.exposure_obj);
                    } else if(data.data.bet_type == 'Bookmaker') {
                        this.updateBookmakerBook(data.data.bet, data.data.current_parent);
                    } else {
                        this.updateEventMarketsBook(data.data.bet, data.data.current_parent);
                    }
                    this.pushBetsBetsObject(data.data.bet_type, data.data.bet);
                } else {
                    if(this.opened_modal.type == 'B' || this.opened_modal.type == 'M') { //book modal
                        var market_object = {
                            match_id: this.event.id,
                            market_id: this.opened_modal.marketId,
                            bookmaker_id: this.opened_modal.bookmaker_id,
                            user_id: this.auth.id,
                            market_type: this.opened_modal.type,
                            fancy_id: this.opened_modal.fancy_id,
                            page_entries: null
                        };
                        if(data.data.bet_type == 'Bookmaker') {
                            this.bookPushBookmakerInPopup(market_object, data.data.bet, data.data.current_parent)
                        } else {
                            this.bookPushExchangeInPopup(market_object, data.data.bet, data.data.current_parent)
                        }
                        // this.callBookApi(market_object);
                    } else if(this.auto_refresh_bets && (this.select_page_markets_bets == null || (this.select_page_markets_bets != null && this.select_page_markets_bets == 1))) {
                        // this.betsModelOpen(this.select_page_markets_bets, false); //bets modal
                        this.betsPushInModal(data.data.bet); //bets modal
                    }
                }
            }
        });

        await Echo.channel("refesh_my_market." + this.auth.session_token).listen("BroadcastBetPlacedEvent", (data) => {
            if(data.data != null && data.data.type == 'sports' && data.data.match_id == this.event.id) {
                if(this.opened_modal == null) {
                    // setTimeout(function() {
                    //     if(data.data.bet_type == 'Fancy' && this.fancyBets.find(x => x.id === data.data.item_id) == undefined) {
                    //         this.getAllMarketsFancys();
                    //         this.showMyBooksAndBets();
                    //     } else if(data.data.bet_type != 'Fancy' && this.matchBets.find(x => x.id === data.data.item_id) == undefined) {
                    //         this.getParentMarketBooks();
                    //         this.showMyBooksAndBets();
                    //     }
                    // }.bind(this), 3000);
                    if(data.data.bet_type == 'Fancy') {
                        this.updateFancyExposure(data.data.exposure_obj);
                    } else if(data.data.bet_type == 'Bookmaker') {
                        this.updateBookmakerBook(data.data.bet, data.data.current_parent);
                    } else {
                        this.updateEventMarketsBook(data.data.bet, data.data.current_parent);
                    }
                    this.pushBetsBetsObject(data.data.bet_type, data.data.bet);
                } else {
                    if(this.opened_modal.type == 'B' || this.opened_modal.type == 'M') { //book modal
                        var market_object = {
                            match_id: this.event.id,
                            market_id: this.opened_modal.marketId,
                            bookmaker_id: this.opened_modal.bookmaker_id,
                            user_id: this.auth.id,
                            market_type: this.opened_modal.type,
                            fancy_id: this.opened_modal.fancy_id,
                            page_entries: null
                        };
                        if(data.data.bet_type == 'Bookmaker') {
                            this.bookPushBookmakerInPopup(market_object, data.data.bet, data.data.current_parent)
                        } else {
                            this.bookPushExchangeInPopup(market_object, data.data.bet, data.data.current_parent)
                        }
                        // this.callBookApi(market_object);
                    } else if(this.auto_refresh_bets && (this.select_page_markets_bets == null || (this.select_page_markets_bets != null && this.select_page_markets_bets == 1))) {
                        // this.betsModelOpen(this.select_page_markets_bets, false); //bets modal
                        this.betsPushInModal(data.data.bet); //bets modal
                    }
                }
            }
        });
    },
    updateFancyExposure(exposure_obj) {
        var fancy_expo = this.fancyExposureArr[exposure_obj.fancy_id];
        if(fancy_expo == undefined) {
            this.fancyExposureArr[exposure_obj.fancy_id] = exposure_obj.exposure;
        } else {
            var current_expo = parseInt(fancy_expo) + parseInt(exposure_obj.exposure)
            this.fancyExposureArr[exposure_obj.fancy_id] = current_expo;
        }
    },
    pushBetsBetsObject(bet_type, bet_obj) {
        if(bet_type == 'Fancy') {
            if(this.fancyBets.length >= 200) {
                this.fancyBets.splice(199, 1);
                this.fancyBets.splice(0, 0, bet_obj);
            } else {
                this.fancyBets.splice(0, 0, bet_obj);
            }
            this.fancyBetsPagination.total += 1;
        } else {
            if(this.matchBets.length >= 200) {
                this.matchBets.splice(199, 1);
                this.matchBets.splice(0, 0, bet_obj);
            } else {
                this.matchBets.splice(0, 0, bet_obj);
            }
            this.matchBetsPagination.total += 1;
        }
    },
    betsPushInModal(bet_obj) {
        if(bet_obj.bet_type == 'Fancy' && this.opened_modal.fancy_id != bet_obj.fancy_id) {
            return;
        } else if(bet_obj.bet_type == 'Bookmaker' && this.opened_modal.bookmaker_id != bet_obj.bookmaker_id) {
            return;
        } else if(bet_obj.bet_type != 'Fancy' && bet_obj.bet_type != 'Bookmaker' && this.opened_modal.marketId != bet_obj.match_market_id) {
            return;
        }
        var should_pushed = false;
        if(this.search_bets_market != null && this.search_bets_market != '') {
            if(this.bets_search_by == 'user' && bet_obj.bet_user_id.toLowerCase().includes(this.search_bets_market.toLowerCase())) {
                should_pushed = true;
            } else if (this.bets_search_by == 'selection' && bet_obj.selection.toLowerCase().includes(this.search_bets_market.toLowerCase())) {
                should_pushed = true;
            } else if (this.bets_search_by == 'stake' && bet_obj.stake.toLowerCase().includes(this.search_bets_market.toLowerCase())) {
                should_pushed = true;
            }
        } else {
            should_pushed = true;
        }
        if(should_pushed) {
            if(this.opened_modal.user_bet.data.length >= 200) {
                this.opened_modal.user_bet.data.splice(199, 1);
                this.opened_modal.user_bet.data.splice(0, 0, bet_obj);
            } else {
                this.opened_modal.user_bet.data.splice(0, 0, bet_obj);
            }
        }
        this.opened_modal.user_bet.total += 1;
    },
    updateBookmakerBook(bet_obj, current_parent) {
        var bookmaker_id = bet_obj.bookmaker_id;
        var bookmaker_obj = this.current_bookmakers.find((bookmaker) => bookmaker.id == bookmaker_id);
        const find_bookmakerbook_index = (element) => element.id == bookmaker_id;
        var bookmakerIndex = this.bookmakersBooks.findIndex(find_bookmakerbook_index);
        var bookmakerBookObject = null;
        var my_pr = current_parent == null ? 100 :  (current_parent.id == this.auth.id ? current_parent.pr_client : current_parent.pr);
        if(bookmakerIndex == -1) {
            var data = {
                id: bookmaker_id,
                real_runners: true,
                runner_1_book: 0,
                runner_2_book: 0,
                runner_3_book: 0
            };
            bookmakerIndex = this.bookmakersBooks.length;
            bookmakerBookObject = data;
        } else {
            bookmakerBookObject = this.bookmakersBooks[bookmakerIndex];
        }
        if (bookmaker_obj.name_1 == bet_obj.selection) {
            if (bet_obj.selection_type == 'back') {
                bookmakerBookObject.runner_1_book = bookmakerBookObject.runner_1_book + bet_obj.p_l*(my_pr/100);
                bookmakerBookObject.runner_2_book = bookmakerBookObject.runner_2_book - bet_obj.stake*(my_pr/100);
                if (bookmaker_obj.name_3 != null && bookmaker_obj.name_3 != '') {
                    bookmakerBookObject.runner_3_book = bookmakerBookObject.runner_3_book - bet_obj.stake*(my_pr/100);
                }
            } else if(bet_obj.selection_type == 'lay') {
                bookmakerBookObject.runner_1_book = bookmakerBookObject.runner_1_book - bet_obj.p_l*(my_pr/100);
                bookmakerBookObject.runner_2_book = bookmakerBookObject.runner_2_book + bet_obj.stake*(my_pr/100);
                if (bookmaker_obj.name_3 != null && bookmaker_obj.name_3 != '') {
                    bookmakerBookObject.runner_3_book = bookmakerBookObject.runner_3_book + bet_obj.stake*(my_pr/100);
                }
            }
        } else if (bookmaker_obj.name_2 == bet_obj.selection) {
            if (bet_obj.selection_type == 'back') {
                bookmakerBookObject.runner_1_book = bookmakerBookObject.runner_1_book - bet_obj.stake*(my_pr/100);
                bookmakerBookObject.runner_2_book = bookmakerBookObject.runner_2_book + bet_obj.p_l*(my_pr/100);
                if (bookmaker_obj.name_3 != null && bookmaker_obj.name_3 != '') {
                    bookmakerBookObject.runner_3_book = bookmakerBookObject.runner_3_book - bet_obj.stake*(my_pr/100);
                }
            } else if(bet_obj.selection_type == 'lay') {
                bookmakerBookObject.runner_1_book = bookmakerBookObject.runner_1_book + bet_obj.stake*(my_pr/100);
                bookmakerBookObject.runner_2_book = bookmakerBookObject.runner_2_book - bet_obj.p_l*(my_pr/100);
                if (bookmaker_obj.name_3 != null && bookmaker_obj.name_3 != '') {
                    bookmakerBookObject.runner_3_book = bookmakerBookObject.runner_3_book + bet_obj.stake*(my_pr/100);
                }
            }
        } else if (bookmaker_obj.name_3 == bet_obj.selection) {
            if (bet_obj.selection_type == 'back') {
                bookmakerBookObject.runner_1_book = bookmakerBookObject.runner_1_book - bet_obj.stake*(my_pr/100);
                bookmakerBookObject.runner_2_book = bookmakerBookObject.runner_2_book - bet_obj.stake*(my_pr/100);
                if (bookmaker_obj.name_3 != null && bookmaker_obj.name_3 != '') {
                    bookmakerBookObject.runner_3_book = bookmakerBookObject.runner_3_book + bet_obj.p_l*(my_pr/100);
                }
            } else if(bet_obj.selection_type == 'lay') {
                bookmakerBookObject.runner_1_book = bookmakerBookObject.runner_1_book + bet_obj.stake*(my_pr/100);
                bookmakerBookObject.runner_2_book = bookmakerBookObject.runner_2_book + bet_obj.stake*(my_pr/100);
                if (bookmaker_obj.name_3 != null && bookmaker_obj.name_3 != '') {
                    bookmakerBookObject.runner_3_book = bookmakerBookObject.runner_3_book - bet_obj.p_l*(my_pr/100);
                }
            }
        }
        this.bookmakersBooks[bookmakerIndex] = bookmakerBookObject;
    },
    async bookPushBookmakerInPopup(book_object, bet_obj, current_parent) {
        var bookmaker_id = bet_obj.bookmaker_id;
        if(book_object.bookmaker_id != bookmaker_id) {
            return;
        }
        var bookmaker_obj = this.current_bookmakers.find((bookmaker) => bookmaker.id == bookmaker_id);
        if(bookmaker_obj == undefined) {
            // this.$toasted.error('Plese refresh page !', { icon: "not_interested" })
            return;
        }
        var marketBookObjectIndex = -1;
        if(current_parent != null) {
            marketBookObjectIndex = this.opened_modal.user_book.findIndex((element) => element.id == current_parent.id);
        }
        var bookmakerBookObject = null;
        if(marketBookObjectIndex == -1) {
            marketBookObjectIndex = this.opened_modal.user_book.length;
            bookmakerBookObject = {client_runner_books: {runner_1_book: 0, runner_2_book: 0}, id: current_parent.id, is_parent: true, user: current_parent.userid};
            if(bookmaker_obj.name_3 != null) {
                bookmakerBookObject.client_runner_books.runner_3_book = 0;
            }
        } else {
            bookmakerBookObject = this.opened_modal.user_book[marketBookObjectIndex];
        }
        var my_pr = current_parent == null ? 100 :  (current_parent.id == this.auth.id ? current_parent.pr_client : current_parent.pr);
        if (bookmaker_obj.name_1 == bet_obj.selection) {
            if (bet_obj.selection_type == 'back') {
                bookmakerBookObject.client_runner_books.runner_1_book = bookmakerBookObject.client_runner_books.runner_1_book + bet_obj.p_l*(my_pr/100);
                bookmakerBookObject.client_runner_books.runner_2_book = bookmakerBookObject.client_runner_books.runner_2_book - bet_obj.stake*(my_pr/100);
                if (bookmaker_obj.name_3 != null && bookmaker_obj.name_3 != '') {
                    bookmakerBookObject.client_runner_books.runner_3_book = bookmakerBookObject.client_runner_books.runner_3_book - bet_obj.stake*(my_pr/100);
                }
            } else if(bet_obj.selection_type == 'lay') {
                bookmakerBookObject.client_runner_books.runner_1_book = bookmakerBookObject.client_runner_books.runner_1_book - bet_obj.p_l*(my_pr/100);
                bookmakerBookObject.client_runner_books.runner_2_book = bookmakerBookObject.client_runner_books.runner_2_book + bet_obj.stake*(my_pr/100);
                if (bookmaker_obj.name_3 != null && bookmaker_obj.name_3 != '') {
                    bookmakerBookObject.client_runner_books.runner_3_book = bookmakerBookObject.client_runner_books.runner_3_book + bet_obj.stake*(my_pr/100);
                }
            }
        } else if (bookmaker_obj.name_2 == bet_obj.selection) {
            if (bet_obj.selection_type == 'back') {
                bookmakerBookObject.client_runner_books.runner_1_book = bookmakerBookObject.client_runner_books.runner_1_book - bet_obj.stake*(my_pr/100);
                bookmakerBookObject.client_runner_books.runner_2_book = bookmakerBookObject.client_runner_books.runner_2_book + bet_obj.p_l*(my_pr/100);
                if (bookmaker_obj.name_3 != null && bookmaker_obj.name_3 != '') {
                    bookmakerBookObject.client_runner_books.runner_3_book = bookmakerBookObject.client_runner_books.runner_3_book - bet_obj.stake*(my_pr/100);
                }
            } else if(bet_obj.selection_type == 'lay') {
                bookmakerBookObject.client_runner_books.runner_1_book = bookmakerBookObject.client_runner_books.runner_1_book + bet_obj.stake*(my_pr/100);
                bookmakerBookObject.client_runner_books.runner_2_book = bookmakerBookObject.client_runner_books.runner_2_book - bet_obj.p_l*(my_pr/100);
                if (bookmaker_obj.name_3 != null && bookmaker_obj.name_3 != '') {
                    bookmakerBookObject.client_runner_books.runner_3_book = bookmakerBookObject.client_runner_books.runner_3_book + bet_obj.stake*(my_pr/100);
                }
            }
        } else if (bookmaker_obj.name_3 == bet_obj.selection) {
            if (bet_obj.selection_type == 'back') {
                bookmakerBookObject.client_runner_books.runner_1_book = bookmakerBookObject.client_runner_books.runner_1_book - bet_obj.stake*(my_pr/100);
                bookmakerBookObject.client_runner_books.runner_2_book = bookmakerBookObject.client_runner_books.runner_2_book - bet_obj.stake*(my_pr/100);
                if (bookmaker_obj.name_3 != null && bookmaker_obj.name_3 != '') {
                    bookmakerBookObject.client_runner_books.runner_3_book = bookmakerBookObject.client_runner_books.runner_3_book + bet_obj.p_l*(my_pr/100);
                }
            } else if(bet_obj.selection_type == 'lay') {
                bookmakerBookObject.client_runner_books.runner_1_book = bookmakerBookObject.client_runner_books.runner_1_book + bet_obj.stake*(my_pr/100);
                bookmakerBookObject.client_runner_books.runner_2_book = bookmakerBookObject.client_runner_books.runner_2_book + bet_obj.stake*(my_pr/100);
                if (bookmaker_obj.name_3 != null && bookmaker_obj.name_3 != '') {
                    bookmakerBookObject.client_runner_books.runner_3_book = bookmakerBookObject.client_runner_books.runner_3_book - bet_obj.p_l*(my_pr/100);
                }
            }
        }
        if(this.opened_modal.total[0] == undefined) {
            this.opened_modal.total[0] = bookmakerBookObject.client_runner_books.runner_1_book;
        } else {
            this.opened_modal.total[0] += bookmakerBookObject.client_runner_books.runner_1_book;
        }
        if(this.opened_modal.total[1] == undefined) {
            this.opened_modal.total[1] = bookmakerBookObject.client_runner_books.runner_2_book;
        } else {
            this.opened_modal.total[1] += bookmakerBookObject.client_runner_books.runner_2_book;
        }
        if (bookmaker_obj.name_3 != null && bookmaker_obj.name_3 != '') {
            if(this.opened_modal.total[2] == undefined) {
                this.opened_modal.total[2] = bookmakerBookObject.client_runner_books.runner_3_book;
            } else {
                this.opened_modal.total[2] += bookmakerBookObject.client_runner_books.runner_3_book;
            }
        }
        this.opened_modal.user_book[marketBookObjectIndex] = bookmakerBookObject;
    },
    async bookPushExchangeInPopup(book_object, bet_obj, current_parent) {
        var match_market_id = bet_obj.match_market_id;
        if(book_object.market_id != match_market_id) {
            return;
        }
        var marketObject = this.markets.find((element) => element.id == match_market_id);
        if(marketObject == undefined) {
            return;
        }
        var marketBookObjectIndex = this.opened_modal.user_book.findIndex((element) => element.id == current_parent.id);
        var marketBookObject = null;
        if(marketBookObjectIndex == -1) {
            marketBookObjectIndex = this.opened_modal.user_book.length;
            marketBookObject = {client_runner_books: [], id: current_parent.id, is_parent: true, user: current_parent.userid};
        } else {
            marketBookObject = this.opened_modal.user_book[marketBookObjectIndex];
        }
        var total = this.opened_modal.total;
        var my_pr = current_parent == null ? 100 :  (current_parent.id == this.auth.id ? current_parent.pr_client : current_parent.pr);
        await marketObject.runners.forEach(function(value, i) {
            var back_pl = 0;
            var lay_pl = 0;
            var back_stake = 0;
            var lay_stake = 0;
            if(bet_obj.selection == value && bet_obj.selection_type == 'back') {
                back_pl = bet_obj.p_l*(my_pr/100);
            } else if(bet_obj.selection != value && bet_obj.selection_type == 'back') {
                back_stake = bet_obj.stake*(my_pr/100);
            }
            if(bet_obj.selection == value && bet_obj.selection_type == 'lay') {
                lay_pl = bet_obj.p_l*(my_pr/100);
            } else if(bet_obj.selection != value && bet_obj.selection_type == 'lay') {
                lay_stake = bet_obj.stake*(my_pr/100);
            }
            marketBookObject.client_runner_books[i] = marketBookObject.client_runner_books[i] == undefined ? 0 : marketBookObject.client_runner_books[i];
            marketBookObject.client_runner_books[i] = marketBookObject.client_runner_books[i] + back_pl + lay_stake;
            marketBookObject.client_runner_books[i] = marketBookObject.client_runner_books[i] - lay_pl - back_stake;
            total[i] = total[i] == undefined ? 0 : total[i];
            total[i] = total[i] + back_pl + lay_stake;
            total[i] = total[i] - lay_pl + back_stake;
        });
        this.opened_modal.user_book.total = total;
        this.opened_modal.user_book[marketBookObjectIndex] = marketBookObject;
    },
    async updateEventMarketsBook(bet_obj, current_parent) {
        var match_market_id = bet_obj.match_market_id;
        const find_market_book_index = (element) => element.id == match_market_id;
        var marketIndex = this.markets.findIndex(find_market_book_index);
        var marketBookObject = null;
        var my_pr = current_parent == null ? 100 :  (current_parent.id == this.auth.id ? current_parent.pr_client : current_parent.pr);
        if(marketIndex == -1) {
            return;
        } else {
            marketBookObject = this.markets[marketIndex];
        }
        await marketBookObject.runners.forEach(function(value, i) {
            var back_pl = 0;
            var lay_pl = 0;
            var back_stake = 0;
            var lay_stake = 0;
            if(bet_obj.selection == value && bet_obj.selection_type == 'back') {
                back_pl = bet_obj.p_l*(my_pr/100);
            } else if(bet_obj.selection != value && bet_obj.selection_type == 'back') {
                back_stake = bet_obj.stake*(my_pr/100);
            }
            if(bet_obj.selection == value && bet_obj.selection_type == 'lay') {
                lay_pl = bet_obj.p_l*(my_pr/100);
            } else if(bet_obj.selection != value && bet_obj.selection_type == 'lay') {
                lay_stake = bet_obj.stake*(my_pr/100);
            }
            marketBookObject.runner_books[i] = marketBookObject.runner_books[i] + back_pl + lay_stake;
            marketBookObject.runner_books[i] = marketBookObject.runner_books[i] - lay_pl - back_stake;
            marketBookObject.is_beting = true;
        });
        this.markets[marketIndex] = marketBookObject;
    },
    showMyBooksAndBets() {
        if(this.$router.history.current.path.includes('/my-market/')) {
            var current_user_id = this.current_user != null && this.current_user != "" ? this.current_user.id : this.auth._role == "is_manager" ? 1 : this.auth.id;
            let url = "/show-my-bets-by-event";
            axios.post(url, { match_id: this.event.id, user_id: current_user_id}).then((res) => {
                if(res.data.errorStatus) {
                    this.$toasted.error('Error occured! Please refresh page.', { icon: "not_interested" });
                } else {
                    this.matchBets = res.data.matchBets.data;
                    this.fancyBets = res.data.fancyBets.data;
                    this.matchBetsPagination = res.data.matchBets;
                    this.fancyBetsPagination = res.data.fancyBets;
                }
            }).catch((e) => {
                this.$toasted.error('Please refresh page!', { icon: "not_interested" });
            });
        }
    },
    getChildBooks(user) {
      window.open(this.origin_path + "my-market/" + this.event.slug + "/" + user.id, "_blank");
    },
    getParentMarketBooks() {
        if(this.$router.history.current.path.includes('/my-market/')) {
            var id = this.auth.id;
            if (this.auth._role == "is_manager") {
                id = 1;
            }
            var current_user_id = this.current_user != null && this.current_user != "" ? this.current_user.id : this.auth._role == "is_manager" ? 1 : this.auth.id;
            axios.post("/show-my-childs-books-and-bets-by-event", { match_id: this.event.id, user_id: current_user_id}).then((res) => {
            if(res.data.errorStatus) {
              this.$toasted.error('Error occured! Please refresh page.', { icon: "not_interested" });
            } else {
              this.clients = res.data.clients;
              this.bookmakersBooks = res.data.bookmakersBooks;
              if (res.data.mainMarketBooks.length > 0) {
                for (var i = 0; i < this.markets.length; i++) {
                  var resMarket = _.filter(res.data.mainMarketBooks, {
                    market_id: this.markets[i].market_id,
                  });
                  if (resMarket.length > 0) {
                    resMarket = resMarket[0];
                    this.markets[i].runner_books = resMarket.runner_books;
                  }
                }
              }
            }
          }).catch((e) => {
            console.log(e.response);
          });
        }
    },
    async showFancyBook(f_id) {
      this.fancyBook = [];
      this.open_modal = false;
      var auth_user_id = await this.getCurrentAuth();
      axios
        .post("/get-fancy-book", {
          id: f_id,
          user_id: auth_user_id,
          market_type: "F",
        })
        .then((res) => {
          if (res.data.errorStatus) {
            this.$toasted.error(res.data.message, { icon: "not_interested" });
          } else {
            let array_of_book = Object.keys(res.data.book).map((key) => {
              return res.data.book[key];
            });
            this.fancyBook = array_of_book;
          }
          this.open_modal = true;
        })
        .catch((e) => {
          this.$toasted.error('Something went wrong !', { icon: "not_interested" });
          console.log(e.response);
        });

      this.$modal.show("book-modal");
    },
    getBookmakerBooks(id,selection_id) {
      var data = {
        is_exists: false,
      };
      if (this.bookmakersBooks.length > 0) {
        var bookmaker = _.filter(this.bookmakersBooks, { id: id })[0];
        if (bookmaker != null && bookmaker != "undefined") {
          data.is_exists = true;
          data.runner_book = 0;
          var bookmaker_selection_books = _.filter(bookmaker.runner_books, { selection_id: selection_id })[0];
          if (bookmaker_selection_books != null && bookmaker_selection_books != "undefined") {
            data.runner_book = bookmaker_selection_books.pl;
          }
        }
      }
      return data;
    },
  },
};
</script>
<style>
    #bets_fancy_bookmaker_market_modal .v--modal-box.v--modal {
        left: 5% !important;
        width: 90vw !important;
        height: 90vh !important;
        top: 30px !important;
    }
    #book_bookmaker_market_modal .v--modal-box.v--modal {
        left: 5% !important;
        width: 90vw !important;
        height: 90vh !important;
        top: 30px !important;
    }
    #fancy_book_modal .v--modal-box.v--modal {
        height: 90vh !important;
        top: 30px !important;
    }
</style>
<style scoped>
.event-detail-manage-main-block {
  height: auto !important;
  padding: 10px 14px;
  line-height: 1;
}
.event-detail-manage-main-block .manage-dtl-block:first-child {
  margin-right: 15px;
}
.event-detail-manage-main-block .manage-dtl-block .button-toggle-wrap {
  top: -2px;
  margin-left: 5px;
}
.table th,
.table td {
  white-space: nowrap;
  border: 1px solid #ccc;
}
.thead {
  background-color: #eee;
}
.section-heading-block {
  padding: 0 10px !important;
}
/*@media (min-width: 1280px) {
    .content-body-main-block {
      max-width: calc(100% - 400px);
    }
    .content-body-sidebar {
      width: 400px;
    }
  }*/
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
  overflow-x: auto;
  overflow-y: auto;
  margin-bottom: 10px;
}
.vue-modal-sub-header {
  box-shadow: 0 5px 20px rgba(0, 0, 0, 0.1);
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
  border-bottom: 1px solid #eee;
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
@media (max-width: 1380px) {
  .content-body-sidebar-no-scroll.content-body-sidebar {
    display: none;
  }
  .my-market-event-detail-page .content-body-sidebar-no-scroll.content-body-sidebar,
  .event-new-test.content-body-sidebar-no-scroll.content-body-sidebar {
    display: block;
    width: 35%;
  }
  .menu-is-toggled .event-test-new.content-body-outer {
    padding-right: 0;
  }
  .hide-on-medium {
    display: none;
  }
}
@media (max-width: 1099px) {
  .event-new-test.content-body-sidebar-no-scroll.content-body-sidebar {
    display: none;
    width: 100%;
  }
  .hide-on-medium {
    display: block;
  }
}
.modales {
  pointer-events: auto;
}
.modal-content {
  pointer-events: all !important;
}
.modal-search-box {
  width: 45% !important;
  height: calc(1.5em + 0.75rem + -1px) !important;
}
.live-score-board-tab .active h5 {
  color: #db0a40;
}
.scoreboard-time {
  font-weight: bold;
}
.table {
  white-space: nowrap;
}
.timer-box-section {
  margin: -13px 25px 0px 25px;
}
.timer-box-h-tag {
  color: #212529;
  background: #f39ba1;
  padding: 10px 10px 10px 10px;
  border-radius: 6px;
}
.white-text {
  color: white;
}
.ml-1 {
  margin-left: -1.75rem !important;
}
.background-balck {
  background: black;
}
.font-size-medium {
  font-size: 20px;
}
.green-text {
  color: #22bb92;
}
.black-text {
  color: black;
  white-space: nowrap;
}
.background-light-gray {
  background: lightgray;
}
.font-weight-light {
  font-weight: 100;
}
.red-text {
  color: #db0a40;
}
.cursor-pointer {
  cursor: pointer;
}
.modal-body.scroll-model {
    padding-bottom: 320px !important;
}
.modal-close {
    margin: -1rem -1rem -1rem -1rem;
}
.form-control-sm {
    width: 50%;
}
.search-btn-style, .search-btn-style:focus {
    background: darkgrey;
    border-radius: 0 3px 3px 0;
}
.btm-padding { margin-bottom: 5px; }
</style>
