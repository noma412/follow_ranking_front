<template>
  <section class="follow">
    <h1>{{ msg }}</h1>
    <section class="main_area">
      <div class="main">
        <div class="img_area">
          <a href=""
            ><img
              src="https://pbs.twimg.com/profile_images/1265790210010300417/xL_D53Kx_bigger.jpg"
              alt
          /></a>
        </div>
        <div class="contents_area">
          <div class="title_area">
            <p>ノマ@WEB系エンジニア</p>
            <p>@iroiroha412</p>
            <p>15:30</p>
          </div>
          <div class="message_area">
            <p>
              ツイート本文ツイート本文ツイート本文ツイート本文ツイート本文ツイート本文ツイート本文ツイート本文ツイート本文ツイート本文
            </p>
            <!-- 画像とか動画は余裕があれば追加する -->
          </div>
          <div class="icon_area">
            <img src="../assets/img/comment.svg" alt /><img
              src="../assets/img/retweet.svg"
              alt
            /><img src="../assets/img/iine.svg" alt />
          </div>
        </div>
      </div>
    </section>
  </section>
</template>

<script>
export default {
  name: "follow",
  props: {
    msg: String,
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.follow {
  padding: 40px 20px 0;
  h1 {
    text-align: center;
    font-size: 20px;
    font-weight: bold;
  }
  .main_area {
    width: 800px;
    max-width: 100%;
    margin: 28px auto 0;
    padding: 12px 12px 12px 20px;
    font-size: 16px;
  }
  .main {
    display: flex;
  }
  .title_area {
    p {
      line-height: 1.5;
      display: inline-block;
    }
    p:first-of-type {
      font-weight: bold;
    }
    p:not(:first-of-type) {
      color: rgb(100, 120, 135);
      margin-left: 4px;
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
  .message_area {
    line-height: 1.3125;
    margin-bottom: 12px;
  }
  .icon_area {
    display: flex;
    justify-content: space-between;
    width: 50%;
    img {
      width: 20px;
    }
  }
}
</style>
