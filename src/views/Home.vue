<template>
  <div class="home-container">
    <div style="width:100%;height:30px;background:#f1f1f1;margin:0px 0 20px 0;">
      <div>
        <img
          style="height:30px;margin:0 0 0 0;float:left"
          :src="require('../assets/images/laba.png')"
        />
        <span
          style="font-family:PingFangSC-Regular;color: #373737;line-height:30px;float:left"
        >【公告】 建议使用谷歌浏览器或360浏览器最新版极速模式访问，以获得更佳体验！</span>
      </div>
    </div>
    <el-breadcrumb separator="/" class="breadcrumb">
      <el-breadcrumb-item>您的位置：首页</el-breadcrumb-item>
    </el-breadcrumb>
    <div class="home-content">
      <div class="home-nav">
        <div class="left">
          <div class="left-first">
            <div>简历看板</div>
            <div @click="moreResume" style="font-size:14px;cursor:pointer">更多》</div>
          </div>
          <div class="left-second">
            <ul>
              <li>
                <span>本日新增</span>
                <span style="color:#FF7152;font-size:24px">{{this.incrementBoardList.incrementDaily}}</span>
              </li>
              <li>
                <span>待处理</span>
                <span style="color:#FF7152;font-size:24px">{{this.incrementBoardList.toProcessNum}}</span>
              </li>
            </ul>
          </div>
        </div>
        <div class="left">
          <div class="left-first">
            <div>职位看板</div>
            <div @click="morePosition" style="font-size:14px;cursor:pointer">更多》</div>
          </div>
          <div class="left-second">
            <ul>
              <li>
                <span>已上线</span>
                <span style="color:#FF7152;font-size:24px">{{onlineNums}}</span>
              </li>
              <li>
                <span>待上线</span>
                <span style="color:#FF7152;font-size:24px">{{editingNums}}</span>
              </li>
              <li>
                <span>审核中</span>
                <span style="color:#FF7152;font-size:24px">{{auditingNums}}</span>
              </li>
              <li>
                <span>审核未通过</span>
                <span style="color:#FF7152;font-size:24px">{{auditFailedNums}}</span>
              </li>
              <li>
                <span>已下线</span>
                <span style="color:#FF7152;font-size:24px">{{offlineNums}}</span>
              </li>
            </ul>
          </div>
        </div>

        <div class="left">
          <div class="left-first">
            <div>面试日历</div>
            
          </div>
          <div class="left-second">
            <ul>
              <li>
                <span>待接收</span>
                <span style="color:#FF7152;font-size:24px">{{interviewBoardList.toBeAcceptedNum}}</span>
              </li>
              <li>
                <span>待面试</span>
                <span style="color:#FF7152;font-size:24px">{{interviewBoardList.toBeInterviewedNum}}</span>
              </li>
              <li>
                <span>已完成</span>
                <span style="color:#FF7152;font-size:24px">{{interviewBoardList.completedNum}}</span>
              </li>
              <li>
                <span>已取消</span>
                <span
                  style="color:#FF7152;font-size:24px"
                >{{interviewBoardList.toCancelTheInterviewNum}}</span>
              </li>
              <li>
                <span>已拒绝</span>
                <span style="color:#FF7152;font-size:24px">{{interviewBoardList.refusedNum}}</span>
              </li>
              <li>
                <span>已失效</span>
                <span
                  style="color:#FF7152;font-size:24px"
                >{{interviewBoardList.hasBeenEffectiveNum}}</span>
              </li>
            </ul>
          </div>
          <div class="left-third">
            <el-calendar style="margin:20px 20px 20px 35px" v-model="interviewTime"></el-calendar>
            <div class="position">
              <div style="text-align:center">
                当日面试安排
                <span style="color: #2D72E3;">{{interview.total}}</span> 场
              </div>
              <div class="position-second">
                <div v-for="(item,index) in interview.list" :key="index">
                  <div style="font-size: 14px;text-align:right">
                    <span>{{item.interviewee}}</span>
                    <span>|</span>
                    <span
                      v-if="item.positionName.length>10"
                    >{{item.positionName.substring(0,10)}}...</span>
                    <span v-else>{{item.positionName}}</span>
                    <span>|</span>
                    <span>{{item.salaryMax}}-{{item.salaryMin}}k</span>
                    <span
                      style="font-family: PingFangSC-Regular;color: #3A81F3;"
                    >{{item.interviewTime|formatDate}}</span>面试
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="home-aside">
        <div class="right">
          <div class="right-first">
            <div>系统通知</div>
            <div style="font-size:14px"></div>
          </div>
          <div class="right-second">
            <div class="notification">
              <div class="noti-head">
                <span>标题</span>
                <span>
                  <el-badge style="padding:0 0 10px 0" :value="200" :max="99"></el-badge>
                </span>
              </div>
              <div class="noti-content">敬请期待</div>
            </div>
            <div class="notification">
              <div class="noti-head">
                <span>标题</span>
                <span>
                  <el-badge style="padding:0 0 10px 0" :value="200" :max="99"></el-badge>
                </span>
              </div>
              <div class="noti-content">敬请期待</div>
            </div>
          </div>
        </div>
        <div class="right" style="height:180px">
          <div class="right-first">
            <div>企业摘要</div>
            <div style="font-size:14px"></div>
          </div>
          <div class="right-second">
            <div class="enterprise">
              <div class="enter-head">
                <img :src="this.avatarUrl" />
                <div>
                  <span>企业信息完整度</span>
                  <el-progress style="margin-left: 20px;" :percentage="companyDetails.completedPercent"></el-progress>
                </div>
              </div>
              <div class="enter-content">
                <p>
                  企业名称：{{companyDetails.fullName}}
                  <span class="tag">{{ }}</span>
                </p>
                <p>企业简称：{{companyDetails.shortName}}</p>
                <p v-if="this.status === 'ONLINE'">企业审核：审核已通过</p>
                <p v-else-if="this.status === 'AUDIT_FAIL'">企业审核：审核未通过</p>
                <p v-else-if="this.status === 'AUDITING'">企业审核：审核中</p>
                <p v-else-if="this.status === 'OFFLINE'">企业审核：已下线</p>
                <p v-else>企业审核：未知</p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Cookies from "js-cookie";
export default {
  name: "home",
  data() {
    return {
      interviewTime: new Date(),
      avatarUrl:'',
      interviewBoardList: {},
      interview: {},
      incrementBoardList:{},
      auditFailedNums: "",
      auditingNums: "",
      editingNums: "",
      offlineNums: "",
      onlineNums: "",
      companyID: "",
      companyDetails: {
        fullName: "",
        shortName: "",
        completedPercent: "",
        logoUrl: ""
      },
      status: ""
    };
  },
  methods: {
    //更多简历
    moreResume() {
      this.$router.push({
        path: "/resume/info"
      });
    },
    //更多职位
    morePosition() {
      this.$router.push({
        path: "/position/info"
      });
    },
    //公司状态
    state() {
      this.$http
        .get("/business-core/companyes/state")
        .then(res => {
          if (res.data.code == "200") {
            this.status = res.data.data;
          } else {
          }
        })
        .catch(error => {});
    },
    //面试看板
    interviewBoard() {
      let params = {
        interviewTime: this.interviewTime.getTime(),
        pageNum: 1,
        pageSize: 10,
        sortBy: null,
        sortOrder: null
      };
      this.$http
        .post("/business-core/dashboard/interview", params)
        .then(res => {
          let response = res.data.data;
          if (res.data.code == "200") {
            this.interviewBoardList = response;
            this.interviewListBoard();
          } else {
          }
        })
        .catch(error => {});
    },
    interviewListBoard() {
      let params = {
        interviewTime: this.interviewTime.getTime(),
        pageNum: 1,
        pageSize: 10,
        sortBy: null,
        sortOrder: null
      };
      this.$http
        .post("/business-core/dashboard/interview/list", params)
        .then(res => {
          let response = res.data.data;
          if (res.data.code == "200") {
            this.interview = response;
          } else {
          }
        })
        .catch(error => {});
    },
    //简历看板
    resumeBoard() {
      this.$http
        .get("/business-core/dashboard/resume")
        .then(res => {
          let response = res.data.data;
          if (res.data.code == "200") {
            this.incrementBoardList = response;
            console.log(this.incrementBoardList)
            // this.incrementDailys = res.data.data.incrementDaily;
            // this.toProcessNums = res.data.data.toProcessNum;
          } else {
          }
        })
        .catch(error => {});
    },
    //职位看板
    positionBoard() {
      this.$http
        .get("/business-core/dashboard/position")
        .then(res => {
          // let responses = res.data.data;
          if (res.data.code == "200") {
            this.auditFailedNums = res.data.data.auditFailedNum;
            this.auditingNums = res.data.data.auditingNum;
            this.editingNums = res.data.data.editingNum;
            this.offlineNums = res.data.data.offlineNum;
            this.onlineNums = res.data.data.onlineNum;
          } else {
          }
        })
        .catch(error => {});
    },
    //公司简历简讯
    companyBrief() {
      this.$http
        .get("/business-core/companyes/brief")
        .then(res => {
          if (res.data.code == "200") {
            this.companyID = res.data.data.id;
            this.companyDetails = res.data.data;
            this.avatarUrl = this.companyDetails.logoUrl;
            console.log(this.avatarUrl)
          } else {
          }
        })
        .catch(error => {});
    }
    // //公司详情
    // companyDetail() {
    //   this.$http
    //     .get(`/business-core/company/${this.companyID}`)
    //     .then(res => {
    //       if (res.data.code == "200") {
    //       } else {
    //       }
    //     })
    //     .catch(error => {
    //       console.log(error);
    //     });
    // },
  },
  watch: {
    interviewTime: function() {
      this.interviewBoard();
    }
  },
  created() {
    // this.signId = this.$route.query.id;
    // this.tel = Cookies.get("tel");
    // this.password = Cookies.get("password");
    // console.log(this.signId,this.tel,this.password)
    // if (this.signId == '1') {

    // }
    let token = Cookies.get("Btoken");

    if (token === undefined) {
      Cookies.set("Btoken", "");
    } else if (token) {
      this.companyBrief();
      this.positionBoard();
      this.resumeBoard();
      this.interviewBoard();
      this.state();
    } else {
      this.$router.push({ path: "/login" });
    }
  }
};
</script>


<style lang="stylus">
.home-container {
  display: flex;
  width: 1200px;
  height: 1150px;
  background-color: white;
  padding: 20px;
  margin: 36px auto 0;
  flex-direction: column;

  .breadcrumb {
    width: 100%;
  }

  .home-content {
    margin-top: 30px;
    display: flex;
    flex-direction: row;

    .home-nav {
      display: flex;
      flex-direction: column;

      .left:nth-child(2) {
        margin: 20px 0 0 0;
      }

      .left:nth-child(3) {
        margin: 20px 0 0 0;
      }

      .left {
        background: #FFFFFF;
        border: 1px solid rgba(244, 244, 244, 1);
        box-shadow: 0px 2px 10px 0px rgba(245, 245, 245, 1);

        .left-first {
          width: 900px;
          height: 50px;
          background: #FAFAFA;
          display: flex;
          flex-direction: row;
          justify-content: space-between;
          line-height: 50px;

          div {
            margin: 0 20px 0 20px;
            font-size: 16px;
            font-family: PingFangSC-Medium;
            color: #222222;
          }
        }

        .left-second {
          width: 900px;
          height: 80px;
          background: #ffffff;
          text-align: center;

          ul {
            margin: 0;
            list-style: none;
            display: flex;
            justify-content: space-around;
            padding: 0;
          }

          li {
            flex: 1;
            display: flex;
            flex-direction: column;
            margin: 10px 0 0 0;

            span:first-child {
              line-height: 27px;
              color: #666;
              font-size: 14px;
            }

            span:last-child {
              line-height: 33px;
              font-size: 24px !important;
              color: #a3292e;
            }
          }
        }

        .left-third {
          width: 900px;
          height: 430px;
          display: flex;
          flex-direction: row;

          .position {
            width: 420px;
            height: 300px;
            margin: 40px 30px 0 0;

            div:nth-child(1) {
              font-family: PingFangSC-Regular;
              color: #222222;
              font-size: 16px;
            }

            .position-second {
              margin: 20px 0 0 0;
              font-family: PingFangSC-Regular;
              color: #454545;

              span {
                padding: 0 6px;
              }
            }
          }

          .el-calendar__body {
            width: 350px;
            height: 300px;
          }

          .el-calendar__header {
            display: flex;
            justify-content: space-between;
            padding: 12px 20px;
            border-bottom: 1px solid #ebeef5;
            width: 350px;
          }

          .el-calendar-table .el-calendar-day {
            box-sizing: border-box;
            padding: 8px;
            height: 45px;
          }
        }
      }
    }

    .home-aside {
      display: flex;
      flex-direction: column;
      margin: 0 0 0 20px;

      .right:nth-child(2) {
        margin: 20px 0 0 0;
      }

      .right {
        background: #FFFFFF;
        border: 1px solid rgba(244, 244, 244, 1);
        box-shadow: 0px 2px 10px 0px rgba(245, 245, 245, 1);

        .right-first {
          width: 280px;
          height: 50px;
          background: #FAFAFA;
          display: flex;
          flex-direction: row;
          justify-content: space-between;
          line-height: 50px;

          div {
            margin: 0 20px 0 20px;
            font-size: 16px;
            color: #373737;
          }
        }
      }

      .right-second {
        width: 280px;
        height: auto;
        border: 1px solid red;
        background: #ffffff;
        border: 1px solid rgba(244, 244, 244, 1);
        box-shadow: 0px 2px 10px 0px rgba(245, 245, 245, 1);

        .notification {
          padding: 0 20px 0 20px;
          cursor: pointer;

          &:first-child {
            border-bottom: solid 1px #eee;
          }

          .noti-head {
            display: flex;
            font-size: 16px;
            justify-content: space-between;
            align-items: center;
          }

          .noti-content {
            color: #666;
            font-size: 12px;
            text-align: left;
            margin: 0 0 15px 0;
          }

          .more {
            color: #ff7152;
          }
        }

        .enterprise {
          margin: 10px;

          .enter-head {
            display: flex;
            flex-direction: row;
            align-items: center;
            justify-content: center;
            margin: 0 0 0 10px;

            img {
              width: 50px;
              height: 50px;
            }

            .el-progress-bar__inner {
              background-color: #02b9b8;
            }

            div {
              text-align: left;
              font-size: 12px;
              color: #999;
              flex: 1;

              span {
                margin-left: 20px;
              }
            }
          }
        }

        .enter-content {
          font-size: 12px;
          color: #333333;
          text-align: left;
          margin-top: 5px;
          margin: 0 0 0 10px;
        }
      }
    }
  }
}
</style>
<style>
.el-table th {
  background: #f1f1f1;
}
.pagination {
  margin: 20px 0 20px 0;
  text-align: center;
}
</style>