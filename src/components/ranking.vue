<template>
  <section class="ranking">
    <h1>{{ msg }}</h1>
    <!-- <section class="tab_area">
      <v-tabs>
        <v-tab>フォロー</v-tab>
        <v-tab>フォロワー</v-tab>
      </v-tabs>
    </section> -->
    <!-- <Datepicker
      :language="ja"
      class="calendar"
      :format="DatePickerFormat"
      :disabled-dates="disabledDates"
      v-model="selectedDate"
      @closed="dateSelected()"
      placeholder="日付"
    ></Datepicker> -->
    <section class="main_area">
      <content-loader
        v-if="isLoading"
        class="main content_loader"
        width="400"
        height="100"
      >
        <rect x="48" y="8" rx="3" ry="3" width="340" height="6" />
        <rect x="48" y="26" rx="3" ry="3" width="80" height="6" />
        <rect x="47" y="52" rx="3" ry="3" width="342" height="6" />
        <rect x="47" y="68" rx="3" ry="3" width="342" height="6" />
        <rect x="47" y="84" rx="3" ry="3" width="120" height="6" />
        <circle cx="20" cy="20" r="20" />
      </content-loader>
      <a
        v-else
        v-for="(tweet, index) in tweets"
        :key="index"
        :href="tweet.url"
        target="_blank"
        class="main"
        v-bind:class="['main_' + index]"
      >
        <div class="number"></div>
        <div class="img_area">
          <img :src="tweet.icon" alt loading="lazy" />
        </div>
        <div class="contents_area">
          <div class="title_area">
            <div>
              <span>{{ tweet.name }}</span>
              <span>@{{ tweet.screen_name }}</span>
              <span>{{ tweet.time }}</span>
            </div>
          </div>
          <div class="message_area">
            <p v-html="tweet.text"></p>
            <div v-if="tweet.img.length > 0" class="message_area_img">
              <img
                v-for="(img, i) in tweet.img"
                :key="i"
                :src="img"
                alt
                loading="lazy"
              />
            </div>
            <div v-if="tweet.quote" class="quote">
              <div class="quote_header">
                <div class="quote_img">
                  <img :src="tweet.quote.icon" alt loading="lazy" />
                </div>
                <div class="quote_title">
                  <span class="quote_name">{{ tweet.quote.name }}</span>
                  <span class="quote_screen_name">
                    @{{ tweet.quote.screen_name }}
                  </span>
                  <span class="quote_tweet_time">
                    {{ tweet.quote.time }}
                  </span>
                </div>
              </div>
              <div v-html="tweet.quote.text" class="quote_message_area"></div>
            </div>
          </div>
          <div class="icon_area">
            <div>
              <img src="../assets/img/comment.svg" alt /><span>{{
                tweet.retweet_count
              }}</span>
            </div>
            <div>
              <img src="../assets/img/retweet.svg" alt /><span>{{
                tweet.retweet_count
              }}</span>
            </div>
            <div>
              <img src="../assets/img/iine.svg" alt /><span>{{
                tweet.favorite_count
              }}</span>
            </div>
          </div>
        </div>
      </a>
    </section>
    <transition name="fade">
      <div v-show="error" class="dogeza">
        <img src="../assets/img/dogeza.png" alt loading="lazy" />
      </div>
    </transition>
    <transition name="fade">
      <div v-show="scroll" class="scroll_top" @click="scrollTop">▲</div>
    </transition>
  </section>
</template>

<script>
import firebase from 'firebase/app'
import 'firebase/analytics'
import 'firebase/auth'
// import Datepicker from 'vuejs-datepicker'
import { ja } from 'vuejs-datepicker/dist/locale'
import { ContentLoader } from 'vue-content-loader'

export default {
  name: 'ranking',
  data() {
    return {
      provider: '',
      tweets: '',
      msg: '昨日人気だったツイート',
      ja: ja,
      DatePickerFormat: 'yyyy/MM/dd',
      disabledDates: {
        to: '',
        from: '',
      },
      selectedDate: '',
      isLoading: false,
      error: false,
      scroll: false,
    }
  },
  components: {
    // Datepicker,
    ContentLoader,
  },
  created() {
    const firebaseConfig = {
      apiKey: 'AIzaSyCwTvt8OiqbxygXf7h0mTGw3SMC0oq1aqc',
      authDomain: 'twitter-ff-ranking.firebaseapp.com',
      projectId: 'twitter-ff-ranking',
      storageBucket: 'twitter-ff-ranking.appspot.com',
      messagingSenderId: '92341152210',
      appId: '1:92341152210:web:45548cf3a97dbbdcd9c00b',
      measurementId: 'G-G96JRJFNTS',
    }
    firebase.initializeApp(firebaseConfig)
    firebase.auth().onAuthStateChanged((user) => {
      if (user) {
        let to = new Date()
        let from = new Date()
        to.setDate(to.getDate() - 8)
        from.setDate(from.getDate() - 1)
        this.disabledDates.to = to
        this.disabledDates.from = from
        this.selectedDate = from

        this.provider = user.providerData[0]
        this.updateRanking(false)

        this.scrollEvent()
      } else {
        const provider = new firebase.auth.TwitterAuthProvider()
        firebase.auth().signInWithRedirect(provider)
      }
    })
  },
  methods: {
    dateSelected() {
      this.isLoading = true
      this.updateRanking(true)
    },
    async updateRanking(select) {
      this.error = false
      const date = select ? this.selectedDate : new Date()
      if (!date) {
        this.isLoading = false
        return
      }
      if (!select) {
        date.setDate(date.getDate() - 1)
      }
      const year = date.getFullYear()
      let month = date.getMonth() + 1
      month = month < 10 ? '0' + month : month
      const day = date.getDate()
      if (
        localStorage.getItem(`tweets(${year}/${month}/${day})`) !== null &&
        JSON.parse(localStorage.getItem(`tweets(${year}/${month}/${day})`))
          .length > 1
      ) {
        this.tweets = JSON.parse(
          localStorage.getItem(`tweets(${year}/${month}/${day})`)
        )
        setTimeout(() => {
          this.isLoading = false
        }, 200)
        setTimeout(() => {
          this.$emit('load', false)
        }, 400)
      } else {
        const response = await fetch(
          'https://rly7n1jpug.execute-api.ap-northeast-1.amazonaws.com/default/FRB',
          {
            method: 'POST',
            cache: 'no-cache',
            headers: {
              'Content-Type': 'application/json',
            },
            body: JSON.stringify({
              user_id: this.provider.uid,
              date: date,
            }),
          }
        )
        const tweets = await response.json()
        this.isLoading = false
        setTimeout(() => {
          this.$emit('load', false)
        }, 400)
        if (!tweets.error) {
          this.tweets = tweets
          localStorage.setItem(
            `tweets(${year}/${month}/${day})`,
            JSON.stringify(this.tweets)
          )
        } else {
          if (
            tweets.error.includes('invalid json') ||
            tweets.error.includes('Rate limit')
          ) {
            this.$emit(
              'error',
              `APIが限界を迎えました！ごめんなさい！m(_ _)m<br>10分か15分後には復活しているはずです！！！`
            )
          } else {
            this.$emit(
              'error',
              `何らかの理由で通信に失敗しました。。<br>ごめんなさいー！m(_ _)m`
            )
          }
          this.selectedDate = ''
          this.error = true
        }
      }
    },
    scrollTop() {
      window.scrollTo({
        top: 0,
        behavior: 'smooth',
      })
    },
    scrollEvent() {
      const changePoint = 10
      const _this = this
      window.addEventListener('scroll', function() {
        const scrollTop =
          window.pageYOffset || document.documentElement.scrollTop
        if (scrollTop > changePoint) {
          _this.scroll = true
        } else {
          _this.scroll = false
        }
      })
    },
  },
}
</script>

<style scoped lang="scss">
.ranking {
  color: #0f1419;
  padding: 40px 12px 40px 42px;
  position: relative;
  max-width: 1000px;
  margin: 0 auto;
  &.loading_ranking {
    .number {
      display: none;
    }
  }
  h1 {
    text-align: center;
    font-size: 20px;
    font-weight: bold;
    letter-spacing: 0.8px;
  }

  .tab_area {
    max-width: 600px;
    margin: 20px auto 0;
  }

  .main_area {
    max-width: 600px;
    margin: 24px auto 0;
    font-size: 16px;
    border: 1px solid rgb(235, 238, 240);
    border-radius: 4px;
    .main {
      width: 100%;
      display: flex;
      padding: 12px 16px;
      border-bottom: 1px solid rgb(235, 238, 240);
      position: relative;
      transition: background-color 0.2s;
      &:not(.content_loader) {
        cursor: pointer;
      }
      &:hover:not(.content_loader) {
        background-color: rgba(0, 0, 0, 0.03);
      }
      &:last-of-type {
        border-bottom: none;
      }
      .number {
        position: absolute;
      }
    }
    @for $value from 0 to 10 {
      .main_#{$value} .number {
        width: 58px;
        height: 58px;
        top: -24px;
        left: -34px;
        background-image: url('../assets/img/number.png');
        background-position: -82px * $value 0px;
        background-repeat: no-repeat;
        @if $value >= 5 {
          background-position: -82px * ($value - 5) -68px;
        }
      }
    }
    @for $value from 10 to 50 {
      .main_#{$value} .number {
        width: 50px;
        height: 50px;
        top: -22px;
        left: -32px;
        background-color: #ccc;
        border-radius: 50%;
        &::after {
          font-family: '游ゴシック体', 'Yu Gothic', YuGothic, sans-serif;
          content: '#{$value + 1}';
          display: block;
          height: 100%;
          line-height: 50px;
          text-align: center;
          color: white;
          font-size: 24px;
          font-weight: bold;
        }
      }
    }
  }

  .img_area {
    width: 64px;
    flex-shrink: 0;
    img {
      width: 56px;
      border-radius: 50%;
    }
  }

  .title_area {
    span {
      line-height: 1.5;
    }
    span:first-of-type {
      font-weight: bold;
      margin-right: 6px;
      word-break: break-all;
    }
    span:nth-of-type(2) {
      margin-right: 4px;
      word-break: break-all;
    }
    span:not(:first-of-type) {
      color: rgb(100, 120, 135);
      word-break: keep-all;
    }
  }

  .contents_area {
    flex-grow: 1;
  }

  .message_area {
    line-height: 1.3125;
    margin-bottom: 12px;
    & > p {
      margin-bottom: 8px;
    }
    &_img {
      margin-bottom: 12px;
    }
    &_img img {
      width: 100%;
      background-color: rgba(28, 160, 242, 0.05);
      &:not(:last-of-type) {
        padding-bottom: 20px;
      }
    }
    .quote {
      border: 1px solid rgb(196, 207, 214);
      border-radius: 16px;
      padding: 10px 12px;
      font-size: 14px;
      &_header {
        display: flex;
        align-items: center;
        margin-bottom: 6px;
        .quote_img {
          display: inline-block;
          img {
            width: 24px;
            height: 24px;
            border-radius: 50%;
          }
        }
        .quote_title {
          margin-left: 4.5px;
        }
        span:first-of-type {
          font-weight: bold;
          margin-right: 3px;
          word-break: break-all;
        }
        span:nth-of-type(2) {
          margin-right: 3px;
          word-break: break-all;
        }
        span:not(:first-of-type) {
          color: rgb(100, 120, 135);
          word-break: keep-all;
        }
      }
    }
  }

  .icon_area {
    display: flex;
    padding-left: 4px;
    & > div {
      width: 100px;
      & > * {
        vertical-align: middle;
      }
      img {
        width: 20px;
        margin-right: 10px;
      }
      span {
        font-size: 12px;
        color: rgb(91, 112, 131);
      }
    }
  }

  .dogeza {
    margin-top: 60px;
    text-align: center;
    img {
      max-width: 100%;
    }
  }

  .scroll_top {
    width: 52px;
    height: 52px;
    line-height: 50px;
    border-radius: 50%;
    opacity: 0.6;
    background: #1da1f2;
    color: #fff;
    position: fixed;
    right: 48px;
    bottom: 48px;
    z-index: 10;
    text-align: center;
    cursor: pointer;
    transition: opacity 0.2s;
    &:hover {
      opacity: 1;
    }
  }
  .fade-enter-active,
  .fade-leave-active {
    transition: opacity 0.4s;
  }
  .fade-enter,
  .fade-leave-to {
    opacity: 0;
  }
}
@media (max-width: 540px) {
  .ranking {
    padding: 30px 12px;
    h1 {
      font-size: 18px;
      letter-spacing: 0.4px;
    }
    .main_area {
      font-size: 14px;
      margin-top: 20px;
      .main {
        padding: 10px 8px;
        .number {
          transform: scale(0.7);
        }
        .img_area {
          width: 44px;
          margin-right: 6px;
          img {
            width: 100%;
          }
        }
        .icon_area {
          & > div {
            width: 80px;
            @media (max-width: 359px) {
              width: 70px;
            }
            img {
              width: 18px;
            }
          }
        }
        .contents_area {
          .message_area {
            margin-top: 2px;
            margin-bottom: 8px;
            &_img {
              margin-bottom: 8px;
              img:not(:last-of-type) {
                padding-bottom: 8px;
              }
            }
          }
        }
        .quote {
          font-size: 12px;
          padding: 8px;
          &_header {
            margin-bottom: 4px;
          }
          .quote_img img {
            width: 22px;
            height: 22px;
          }
        }
      }
      @for $value from 0 to 10 {
        .main_#{$value} .number {
          top: -26px;
          left: -20px;
        }
      }
      @for $value from 10 to 50 {
        .main_#{$value} .number {
          top: -24px;
          left: -18px;
        }
      }
    }
    .scroll_top {
      right: 24px;
      bottom: 36px;
    }
  }
}
</style>

<style lang="scss">
.ranking {
  .tab_area {
    color: #1da1f2;
    .v-tab {
      width: 50%;
      font-weight: bold;
      font-size: 16px;
      &--active {
        pointer-events: none;
      }
    }
  }
  .vdp-datepicker {
    max-width: 600px;
    margin: 20px auto 0;
    & > div:first-of-type {
      display: flex;
      justify-content: flex-end;
    }
    input {
      border: 1px solid rgb(235, 238, 240);
      border-radius: 8px;
      height: 36px;
      line-height: 36px;
      padding-left: 10px;
      width: 140px;
      cursor: pointer;
      &::placeholder {
        color: #aaa;
        font-size: 15px;
      }
    }
  }
  @media (max-width: 540px) {
    .vdp-datepicker {
      margin-top: 16px;
      input {
        font-size: 14px;
        width: 110px;
        height: 32px;
        border-radius: 6px;
      }
    }
  }
}
</style>
