<template>
  <section class="ranking">
    <h1>{{ msg }}</h1>
    <section class="tab_area">
      <div class="tab"></div>
      <div class="tab"></div>
    </section>
    <section class="main_area">
      <a
        v-for="(tweet, index) in tweets"
        :key="index"
        :href="tweet.url"
        target="_blank"
        class="main"
        v-bind:class="['main_' + index]"
      >
        <div class="number"></div>
        <div class="img_area">
          <img :src="tweet.icon" alt />
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
            <div v-if="tweet.img" class="message_area_img">
              <img :src="tweet.img" alt />
            </div>
            <div v-if="tweet.quoted_status" class="quote">
              <div class="quote_header">
                <div class="quote_img">
                  <img :src="tweet.quoted_status.icon" alt />
                </div>
                <div>
                  <span class="quote_name">{{ tweet.quoted_status.name }}</span>
                  <span class="quote_screen_name">
                    @{{ tweet.quoted_status.screen_name }}
                  </span>
                  <span class="quote_tweet_time">
                    {{ tweet.quoted_status.time }}
                  </span>
                </div>
              </div>
              <div
                v-html="tweet.quoted_status.text"
                class="quote_message_area"
              ></div>
            </div>
          </div>
          <div class="icon_area">
            <!-- <div><img src="../assets/img/comment.svg" alt /></div> -->
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
  </section>
</template>

<script>
import firebase from 'firebase/app'
import 'firebase/analytics'
import 'firebase/auth'
import 'firebase/firestore'

export default {
  name: 'ranking',
  data() {
    return {
      provider: '',
      tweets: '',
      msg: 'ランキング',
    }
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

    firebase.auth().onAuthStateChanged(async (user) => {
      if (user) {
        if (localStorage.getItem('tweets') !== null) {
          this.tweets = JSON.parse(localStorage.getItem('tweets'))
        } else {
          this.provider = user.providerData[0]
          const uid = user.providerData[0].uid
          const response = await fetch('http://192.168.11.2:1000/twitter', {
            method: 'POST',
            cache: 'no-cache',
            headers: {
              'Content-Type': 'application/json',
            },
            body: JSON.stringify({ user_id: uid, span: 1 }),
          })
          const tweets = await response.json()
          this.tweets = tweets
          localStorage.setItem('tweets', JSON.stringify(this.tweets))
        }
      } else {
        const provider = new firebase.auth.TwitterAuthProvider()
        firebase.auth().signInWithRedirect(provider)
      }
    })
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.ranking {
  padding: 40px 10px;
  h1 {
    text-align: center;
    font-size: 20px;
    font-weight: bold;
  }
  .main_area {
    min-width: 340px;
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
      cursor: pointer;
      position: relative;
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
    @for $value from 10 to 500 {
      .main_#{$value} .number {
        width: 50px;
        height: 50px;
        top: -22px;
        left: -32px;
        background-color: #bbb;
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
    }
    span:nth-of-type(2) {
      margin-right: 4px;
    }
    span:not(:first-of-type) {
      color: rgb(100, 120, 135);
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
    }
    .quote {
      border: 1px solid rgb(196, 207, 214);
      border-radius: 16px;
      padding: 12px;
      &_header {
        display: flex;
        align-items: center;
        margin-bottom: 8px;
        .quote_img {
          display: inline-block;
          img {
            width: 24px;
            height: 24px;
            border-radius: 50%;
          }
        }
        span {
          font-size: 14px;
        }
        span:first-of-type {
          font-weight: bold;
          margin-left: 4.5px;
          margin-right: 3px;
        }
        span:nth-of-type(2) {
          margin-right: 3px;
        }
        span:not(:first-of-type) {
          color: rgb(100, 120, 135);
        }
      }
      &_message_area {
        font-size: 14px;
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
}
</style>
