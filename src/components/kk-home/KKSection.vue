<template>
  <div>
    <div ref="head">
      <div id="nav">
        <div id="back" @click="back"><</div>
        <p id="title">{{title}}</p>
        <div id="complete" @click="back">全集</div>
      </div>
      <mt-popup
        v-model="popupVisible"
        position="bottom">
        <div id="popup">
          &nbsp;
          <p id="shin">分享</p>
          <div id="social">
            <div class="dian">
              <img src="../../assets/kkcartoontitle/weichat.png" alt="">
              <div>微信</div>
            </div>
            <div class="dian">
              <img src="../../assets/kkcartoontitle/friend.png" alt="">
              <p>朋友圈</p>
            </div>
            <div class="dian">
              <img src="../../assets/kkcartoontitle/QQ.png" alt="">
              <p>QQ</p>
            </div>
            <div class="dian">
              <img src="../../assets/kkcartoontitle/qqkj.png" alt="">
              <p>QQ空间</p>
            </div>
            <div class="dian">
              <img src="../../assets/kkcartoontitle/weibo.png" alt="">
              <p>微博</p>
            </div>
          </div>
          <div id="collect">
            <div class="dian">
              <img src="../../assets/kkcartoontitle/collect.png" alt="">
              <p>收藏</p>
            </div>
            <div class="dian">
              <img src="../../assets/kkcartoontitle/report.png" alt="">
              <p>举报</p>
            </div>
          </div>
          <!--分割线-->
          <div id="line">&nbsp;</div>
          <div id="abolish" @click="abolish">取消</div>
        </div>
      </mt-popup>
      <mt-popup
        v-model="switchValue"
        position="bottom">
        <div id="switch">
          <div class="tools">
            <div id="left">翻页模式</div>
            <div id="right">当前漫画只适合上下分页哦</div>
          </div>
          <div class="tools">
            <div class="page">
              <p>点击翻页</p>
              <p>打开后可点击屏幕上下方翻页</p>
            </div>
            <div class="pageSwitch">
              <mt-switch v-model="value"></mt-switch>
            </div>
          </div>
          <div class="tools">
            <div class="page">
              <p>夜间模式</p>
            </div>
            <div class="pageSwitch">
              <mt-switch v-model="night"></mt-switch>
            </div>
          </div>
        </div>
      </mt-popup>
    </div>
    <div id="content" @touchmove="move">
      <div class="box" v-for="(k,i) in images">
        <!--<img :src="k" alt="" class="iamges">-->
        <img v-lazy="k" class="images">
      </div>
    </div>
    <div id="rump">
      <div class="zan">
        <img src="../../assets/kkcartoontitle/nozan.png" alt="">
        <p class="text">赞{{zan_count}}</p>
      </div>
      <div class="attention">
        <img src="../../assets/kkcartoontitle/attention.png" alt="">
        <p class="text">关注</p>
      </div>
      <div class="share">
        <img src="../../assets/kkcartoontitle/share.png" alt="">
        <p class="text">分享</p>
      </div>
    </div>
    <div id="cross">
      <div class="previous_posts" @touchend="previous"><&nbsp;上一篇</div>
      <div class="line"></div>
      <div class="next_chapter" @touchend="next">下一篇&nbsp;></div>
    </div>
    <!--作者-->
    <div class="author">
      <p class="sign"></p>
      <div>作者</div>
    </div>
    <!--作者详情-->
    <div id="information">
      <div v-for="(val,index) in authorArr" class="authorList">
        <div class="authorHead">
          <img :src="val.avatar_url" alt="">
        </div>
        <div class="authorName">{{val.nickname}}</div>
      </div>
    </div>
    <!--评论-->
    <div class="author">
      <p class="sign"></p>
      <div>评论</div>
    </div>
    <ul>
      <li v-for="(val,index) in commentsArr" class="comments">
        <img :src="val.root.user.avatar_url" alt="">
        <div class="rightContent">
          <div class="username">{{val.root.user.nickname}}</div>
          <div class="commentext">
            {{val.root.content}}
          </div>
          <!--<p class="children_comments">{{val.children_comments.id}}1111111</p>-->
          <div class="children_comments" v-if="val.children_comments.length>0">
            <div v-for="(v,i) in val.children_comments">
              <span>{{v.user.nickname}}</span>
              :{{v.content}}
            </div>
          </div>
          <!--时间-->
          <div class="time">
            <div class="created">{{val.root.created_at_info}}</div>
            <div class="call" @touchend="call(val.root.id)">{{val.root.likes_count}}</div>
          </div>
        </div>
      </li>
    </ul>
    <!--查看更多评论-->
    <router-link to='/' tag="p" @touchend.native="linkto" id="chakan">查看更多评论&nbsp;></router-link>
    <!--底部发送-->
    <div id="comment" ref="foot">
      <input type="text" placeholder="吐槽神马的尽管来">
      <div id="send">发送</div>
      <div id="foot">
        <div id="chapter">
          <div @touchend="previous"><</div>
          <div id="now">当前话</div>
          <div class="right" tag="span" @touchend="next"> > </div>
        </div>
        <router-link to='/' tag="div" id="discuss" @touchend.native="linkto">
          <div id="comment_count">{{comments}}</div>
        </router-link>
        <div id="share" @touchend="share" @click="shartt">&nbsp;</div>
        <div id="tool" @touchend="tool">&nbsp;</div>
      </div>
    </div>
  </div>
</template>

<script>
  var num = 0
  export default {
    name: '',
    data () {
      return {
        title: '',
        images: [],
        comments: '',
        targed: '',
        zan_count: '',
        authorArr: [],
        commentsArr: [],
        popupVisible: false,
        switchValue: false,
        value: true,
        night: false,
        nextParams: 0,
        previousParams: 0
      }
    },
    methods: {
      previous: function () {
        scrollTo(0, 0)
        let ace = this.previousParams
        let url = {
          url: '/kkv2/comic/' + ace,
          type: 'get',
          success: function (res) {
            this.title = res.data.data.title
            this.images = res.data.data.images
            this.comments = res.data.data.comments_count
            this.targed = res.data.data.id
            this.zan_count = res.data.data.likes_count
            this.authorArr = res.data.data.topic.related_authors
            this.nextParams = res.data.data.next_comic_id
            this.previousParams = res.data.data.previous_comic_id
          },
          failed: function (err) {
            console.log(err)
          }
        }
        this.$request(url)
      },
      next: function () {
        scrollTo(0, 0)
        let ace = this.nextParams
        let url = {
          url: '/kkv2/comic/' + ace,
          type: 'get',
          success: function (res) {
            this.title = res.data.data.title
            this.images = res.data.data.images
            this.comments = res.data.data.comments_count
            this.targed = res.data.data.id
            this.zan_count = res.data.data.likes_count
            this.authorArr = res.data.data.topic.related_authors
            this.nextParams = res.data.data.next_comic_id
            this.previousParams = res.data.data.previous_comic_id
          },
          failed: function (err) {
            console.log(err)
          }
        }
        this.$request(url)
      },
      shartt: function () {
        this.popupVisible = true
        this.$refs.foot.style.display = 'none'
      },
      abolish: function () {
        this.popupVisible = false
        this.$refs.foot.style.display = 'block'
      },
      linkto: function () {
        this.$router.push({ name: 'kkcomment', params: {id: this.targed} })
      },
      back: function () {
        window.history.back()
      },
      call: function (i) {
        console.log(i)
        let url = {
          url: '/kkv2/like/add',
          type: 'post',
          params: {
            app_id: 1000000027,
            kk_c_t: 1511430018881,
            kk_s_t: 1511430016139,
            target_id: i,
            target_type: 'comment',
            tt: false
          },
          success: function (res) {
            console.log(res.data)
          },
          failed: function (err) {
            console.log(err)
          }
        }
        this.$request(url)
      },
      share: function () {
        console.log('点击分享')
      },
      tool: function () {
        this.switchValue = true
        this.$refs.foot.style.display = 'block'
      },
      move: function () {
        var that = this
        if (window.scrollY > 1 && window.scrollY <= 6800) {
          this.$refs.head.style.display = 'none'
          this.$refs.foot.style.display = 'none'
          this.$refs.head.className = ''
        } else if (window.scrollY >= 7000) {
          this.$refs.head.style.display = 'block'
          this.$refs.foot.style.display = 'block'
        } else if (window.scrollY > 5600) {
          if (num === 0) {
            console.log('jin')
            num = 1
            let url = {
              url: '/kkv2/comments/hot_floor_list',
              type: 'get',
              params: {
                target_id: this.$route.params.id,
                target_type: 'comic'
              },
              success: function (res) {
                that.commentsArr = res.data.data.comment_floors
                console.log(res.data.data.comment_floors)
              },
              failed: function (err) {
                console.log(err)
              }
            }
            this.$request(url)
          }
        } else if (window.scrollY === 0) {
          this.$refs.head.style.display = 'block'
        }
      }
    },
    mounted () {
      var that = this
      let ace = that.$route.params.id
      let url = {
        url: '/kkv2/comic/' + ace,
        type: 'get',
        success: function (res) {
          that.title = res.data.data.title
          that.images = res.data.data.images
          that.comments = res.data.data.comments_count
          that.targed = res.data.data.id
          that.zan_count = res.data.data.likes_count
          that.authorArr = res.data.data.topic.related_authors
          that.nextParams = res.data.data.next_comic_id
          that.previousParams = res.data.data.previous_comic_id
          console.log(res.data.data)
        },
        failed: function (err) {
          console.log(err)
        }
      }
      this.$request(url)
    }
  }
</script>

<style scoped lang="less">
  image[lazy=loading] {}
  #nav {
    position: relative;
    height: 40px;
    border-bottom: 1px solid darkgray;
  }
  #back {
    position: absolute;
    left: 15px;
    font-size: 20px;
    line-height: 40px;
  }
  #title {
    font-size: 14px;
    text-align: center;
    position: absolute;
    width: 220px;
    left: 50%;
    line-height: 40px;
    margin-left: -110px;
  }
  #complete {
    position: absolute;
    right: 15px;
    line-height: 40px;
    color: #FFCB57;
  }
  #content {
    width: 100%;
  }
  #content>img {
    width: 100%;
    display: inline-block;
    font-size: 0px;
    margin-bottom: -5px;
  }
  #comment {
    width: 100%;
    height: 90px;
    background-color: white;
    position: fixed;
    bottom: 0;
  }
  input {
    position: absolute;
    width: 70%;
    height: 22px;
    border-radius: 10px;
    outline: none;
    padding-left: 35px;
    border: 1px solid darkgray;
    background: url("../../assets/kkcartoontitle/publish.png") no-repeat 10px;
    top: 10px;
    left:20px;
  }
  #send {
    position: absolute;
    top: 12px;
    right: 30px;
    font-size: 14px;
  }
  #foot {
    position: absolute;
    width: 100%;
    left: 0;
    height: 20px;
    bottom: 14px;
  }
  #now {
    margin-left: 10px;
    margin-right: 10px;
  }
  #chapter {
    position: absolute;
    top: 0px;
    left: 20px;
  }
  #chapter>div {
    display: inline-block;
  }
  #chapter>a {
    color: darkgray;
  }
  .right {
    color: black;
    font-size: 18px;
  }
  #discuss {
    position: absolute;
    top: -4px;
    left: 60px;
    width: 60px;
    height: 30px;
    left: 50%;
    margin-left: -20px;
    /*border: 1px solid red;*/
    background: url("../../assets/kkcartoontitle/discuss.png") no-repeat left 5px;
  }
  #comment_count {
    font-size: 13px;
    color: white;
    text-align: center;
    position: absolute;
    left: 15px;
    width: 36px;
    height: 15px;
    line-height: 15px;
    background-color: red;
    border-radius: 10px;
  }
  #share {
    position: absolute;
    width: 30px;
    background: url("../../assets/kkcartoontitle/share.png") no-repeat;
    left: 67%;
  }
  #tool {
    position: absolute;
    left: 85%;
    width: 30px;
    background: url("../../assets/kkcartoontitle/tool.png") no-repeat;
  }
  #rump {
    position: relative;
    height: 60px;
    border-bottom: 1px solid darkgray;
  }
  #rump>div {
    display: inline-block;
  }
  .zan {
    position: absolute;
    width: 65px;
    left: 10%;
  }
  .zan>img {
    margin-left: 15px;
  }
  .attention {
    position: absolute;
    left: 50%;
    width: 36px;
    margin-left: -15px;
  }
  .attention>img {
    margin-left: 2px;
  }
  .text {
    color: #626262;
    width: 100px;
  }
  .share {
    position: absolute;
    left: 80%;
  }
  .share>img {
    margin-left: 2px;
  }
  #cross {
    position: relative;
    border-bottom: 2px solid darkgray;
    padding-top: 10px;
    padding-bottom: 10px;
    height: 50px;
  }
  #cross>div {
    position: absolute;
    display: inline-block;
  }
  .previous_posts {
    height: 40px;
    width: 80px;
    left: 15%;
    line-height: 50px;
  }
  .next_chapter {
    left: 70%;
    line-height: 50px;
  }
  .line {
    width: 1px;
    height: 50px;
    background-color: darkgray;
    left: 50%;
  }
  .author {
    padding: 5px 10px;
    border-bottom: 1px solid darkgray;
  }
  .sign{
    display: inline-block;
    width: 6px;
    height: 12px;
    background-color: #FCB43C;
    border-top-right-radius: 4px;
    border-top-left-radius: 4px;
    border-bottom-right-radius: 4px;
    border-bottom-left-radius: 4px;
  }
  .author>div {
    display: inline-block;
    font-size: 14px;
  }
  #information {
    position: relative;
    padding: 13px 10px;
  }
  #information>div {
    display: inline-block;
  }
  .authorList {
    position: relative;
    width: 160px;
    height: 40px;
  }
  .authorList>div {
    display: inline-block;
  }
  .authorHead {
    width: 40px;
    height: 40px;
    border-radius: 50%;
    position: absolute;
    left: 0;
    top: 0;
  }
  .authorHead>img {
    width: 100%;
    height: 100%;
    border-radius: 50%;
  }
  .authorName {
    font-size: 13px;
    position: absolute;
    left: 50px;
    top: 12px;
  }
  .comments {
    width: 100%;
    padding-left: 15px;
    padding-top: 15px;
    border-bottom: 1px solid darkgray;
  }
  .comments>img {
    width: 40px;
    height: 40px;
    border-radius: 50%;
    left: 10px;
    top: 10px;
    vertical-align: top;
  }
  .rightContent {
    display: inline-block;
  }
  .username {
    font-size: 13px;
    color: #6D6D6D;
  }
  .commentext {
    width: 320px;
    word-wrap: break-word;
    margin-top: 10px;
  }
  .children_comments {
    background-color: #F6F9FA;
    padding: 7px 10px;
    width: 320px;
    margin-top: 15px;
  }
  .children_comments>div {
    font-size: 13px;
  }
  .children_comments>div>span {
    color: #757575;
    font-size: 13px;
  }
  .time {
    width: 340px;
    overflow: hidden;
    margin-top: 10px;
    color: #A7A7A7;
    font-size: 13px;
  }
  .box {
    background-image: url(../../assets/kk-find/kk-mhbg.jpg);
    background-repeat:no-repeat;
    -webkit-background-size: cover;
    background-size: cover;
    background-position: center center;
  }
  .images {
    width: 100%;
  }
  .created {
    float: left;
  }
  .call {
    float: right;
    padding-left: 15px;
    background: url("../../assets/zan.png") no-repeat left;
  }
  #chakan {
    width: 140px;
    text-align: center;
    margin: auto;
    font-size: 12px;
    color: #289CE0;
    margin-bottom: 100px;
    padding-top: 15px;
  }
  #popup {
    background-color: #E5E3E1;
    width: 432px;
    position: relative;
    height: 280px;
    z-index: 120;
  }
  #shin {
    position: absolute;
    top: 10px;
    width: 100px;
    font-size: 13px;
    text-align: center;
    left: 50%;
    margin-left: -50px;
  }
  #social {
    position: absolute;
    top: 50px;
    left: 30px;
    width: 360px;
    display: flex;
    flex-wrap: wrap;
    flex-direction: row;
    justify-content: space-between;
  }
  .dian {
    text-align: center;
    display: inline-block;
  }
  #collect {
    position: absolute;
    top: 140px;
    left: 30px;
  }
  #collect div:nth-child(2) {
    margin-left: 20px;
  }
  #line {
    position: absolute;
    top: 230px;
    width: 100%;
    height: 1px;
    background-color: darkgray;
  }
  #abolish {
    position: absolute;
    width: 60px;
    left: 50%;
    margin-left: -30px;
    font-size: 20px;
    text-align: center;
    top: 240px;
  }
  #switch {
    width: 432px;
    height: 200px;
    background-color: #FFFFFF;
  }
  .tools {
    overflow: hidden;
    margin-top: 30px;
  }
  #left {
    float: left;
    padding-left: 25px;
  }
  #right {
    float: right;
    font-size: 12px;
    width: 190px;
    color: #C1C4CF;
  }
  .page {
    float: left;
    padding-left: 25px;
  }
  .page p:nth-child(2) {
    font-size: 12px;
    color: #C1C4CF;
  }
  .pageSwitch {
    float: right;
    width: 80px;
  }
</style>
