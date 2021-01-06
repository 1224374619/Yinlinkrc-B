<template>
  <div class="promotionalDetail">
    <div class="title">
      <div class="title-nav">活动详情</div>
    </div>
    <div class="content">
      <div class="content-nav">
        <div>
          <img :src="this.appraiseDetails.activityPosterUrl" />
        </div>
        <div class="content-nava">
          <div>
            {{this.appraiseDetails.activityName}}
            <span>{{this.appraiseDetails.activityRegistrationState|level}}</span>
          </div>
          <div>报名时间：{{this.appraiseDetails.registrationStartTime|formatDateOne}}-{{this.appraiseDetails.registrationEndTime|formatDateOne}}</div>
          <div>活动时间：{{this.appraiseDetails.activityStartTime|formatDateOne}}-{{this.appraiseDetails.activityEndTime|formatDateOne}}</div>
          <div>发布时间：2019.6.18 10:00</div>
          <div v-if="this.appraiseDetails.activityMode === 0">活动地址：线上活动</div>
          <div v-else>活动地址：{{this.appraiseDetails.activityAddress.detail}}</div>
          <div>报名人数：{{this.appraiseDetails.registeredNum}}/{{this.appraiseDetails.registrationNum}}</div>
          <div>
            <button @click="share('https://www.yinlinkrc.com/client-zh/#/appraiseDetail?id='+ id)">分享活动</button>
            <button @click="enroll(id)">报名详情</button>
          </div>
        </div>
      </div>
      <div class="footer">
        <div class="footer-title">活动详情</div>
        <div class="footer-content" v-html="this.appraiseDetails.activityContent"></div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      id: "",
      appraiseDetails: {}
    };
  },
  methods: {
    //分享活动
    share(data) {
      console.log(data)
      let url = data;
      let oInput = document.createElement("input");
      oInput.value = url;
      document.body.appendChild(oInput);
      oInput.select(); // 选择对象;
      console.log(oInput.value);
      document.execCommand("Copy"); // 执行浏览器复制命令
      this.$notify({
        title: "成功",
        message: "复制带剪切板成功",
        type: "success"
      });
      oInput.remove();
    },
    //报名详情
    enroll(res) {
      this.$router.push({
        path: "/unsteady/enroll",
        query: { id: res }
      });
    },
    //活动详情
    appraiseDetail() {
      this.$http
        .get(`/business-core/activity/${this.id}`)
        .then(res => {
          if (res.data.code == 200) {
            this.appraiseDetails = res.data.data;
          } else {
          }
        })
        .catch(error => {});
    }
  },
  created() {
    this.id = this.$route.query.id;
    this.appraiseDetail();
  },
  filters: {
    level(level) {
      var a;
      switch (level) {
        case "REGISTRATION_NOT_STARTED":
          a = "报名未开始";
          break;
        case "REGISTRATION_IN_PROGRESS":
          a = "报名进行中";
          break;
        case "REGISTRATION_IS_UP":
          a = "报名已截止";
          break;
      }
      return a;
    }
  }
};
</script>

<style lang="stylus" scoped>
.promotionalDetail {
  margin: 30px 0 0 0;

  .title {
    width: 963px;
    height: 50px;
    background: #FAFAFA;
    display: flex;
    flex-direction: row;

    .title-nav {
      font-family: PingFangSC-Medium;
      color: #02b9b8;
      font-size: 16px;
      margin: 13px 0 0 20px;
    }
  }

  .content {
    width: 963px;
    height: auto;
    background: #FFFFFF;
    border: 1px solid rgba(246, 246, 246, 1);
    box-shadow: 0px 2px 12px 0px rgba(0, 0, 0, 0.06);

    .content-nav {
      display: flex;
      flex-direction: row;
      margin: 30px 0 0 20px;

      img {
        width: 270px;
        height: 210px;
      }

      .content-nava {
        margin: 0 0 0 25px;

        div:nth-child(1) {
          font-family: PingFangSC-Medium;
          color: #333333;
          font-size: 18px;

          span {
            font-size: 12px;
            color: #ff6600;
          }
        }

        div:nth-child(2) {
          font-family: PingFangSC-Regular;
          color: #333333;
          font-size: 14px;
          margin: 17px 0 0 0;
        }

        div:nth-child(3) {
          font-family: PingFangSC-Regular;
          color: #333333;
          font-size: 14px;
          margin: 7px 0 0 0;
        }

        div:nth-child(4) {
          font-family: PingFangSC-Regular;
          color: #333333;
          font-size: 14px;
          margin: 7px 0 0 0;
        }

        div:nth-child(5) {
          font-family: PingFangSC-Regular;
          color: #333333;
          font-size: 14px;
          margin: 7px 0 0 0;
        }

        div:nth-child(6) {
          font-family: PingFangSC-Regular;
          color: #333333;
          font-size: 14px;
          margin: 7px 0 0 0;
        }

        div:nth-child(7) {
          margin: 7px 0 0 0;

          button:nth-child(1) {
            width: 93px;
            height: 33px;
            background: #02b9b8;
            border: 1px solid #02b9b8;
            border-radius: 5px;
            font-family: PingFangSC-Regular;
            color: #FFFFFF;
            font-size: 18px;
          }

          button:nth-child(2) {
            width: 93px;
            height: 33px;
            background: #ffffff;
            border: 1px solid rgba(50, 124, 243, 1);
            border-radius: 5px;
            font-family: PingFangSC-Regular;
            color: #02b9b8;
            font-size: 18px;
            margin: 0 0 0 20px;
          }
        }
      }
    }

    .footer {
      margin: 70px 20px 0 20px;

      .footer-title {
        font-family: PingFangSC-Regular;
        color: #333333;
        font-size: 16px;
      }

      .footer-content {
        font-family: PingFangSC-Regular;
        color: #333333;
        font-size: 16px;
        margin: 20px 0 30px 0;
      }
    }
  }
}
</style>