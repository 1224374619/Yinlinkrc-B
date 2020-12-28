
<template>
  <div class="containers">
    <!-- <customized-nav class="nav" :ctlHideMenus="true" /> -->
    <div class="popContainer" v-show="writeMessageShow" @click="writeMessageFun($event)">
      <div class="messageMaskContent" ref="msk">
        <div class="navs">微信登录</div>
        <!-- <div >
          <img src="../assets/images/foot-wxs.png" />
        </div>-->
        <remotejs
          src="https://res.wx.qq.com/connect/zh_CN/htmledition/js/wxLogin.js"
        />
        <div class="imgSrc" id="login_container"></div>
        <div class="foot">
          <button>
            请使用微信扫描二维码登录
            “银领人才网”
          </button>
        </div>
      </div>
    </div>
    <div class="body">
      <div class="form">
        <div class="logo">
          <img style="height:40px;margin:57px 0 0 0" :src="require('../assets/images/logo.png')" />
        </div>
        <div class="photo">
          <div style="margin:49px 0 0 174px">
            <img style="height:392px;" :src="require('../assets/images/de.png')" />
          </div>
          <div class="formlet" style="margin:30px 0 0 158px">
            <div class="header">
              <span class="deng">登录</span>
              <el-button
                style="margin:39px 0 0 0;color:#373737;font-size:16px;font-wight:500;text-align:right"
                @click="business"
                type="text"
              ></el-button>
            </div>
            <div class="formls">
              <el-form ref="form" :rules="rules" :model="form" label-width="0px">
                <el-form-item label prop="tel">
                  <el-input
                    prefix-icon="el-icon-mobile-phone"
                    style="width:270px;height:43px;"
                    v-model.number="form.tel"
                    placeholder="请输入登录手机号"
                  ></el-input>
                </el-form-item>
                <el-form-item label prop="password">
                  <el-input
                    prefix-icon="el-icon-lock"
                    :type="show.old?'text':'password'"
                    style="width:270px;height:43px;"
                    placeholder="请输入密码"
                    v-model="form.password"
                  >
                    <img
                      :src="show.old?require('../assets/images/yan.png'):require('../assets/images/bi.png')"
                      slot="suffix"
                      alt
                      style="margin:0 5px 0 0"
                      @click="show.old=!show.old"
                    />
                  </el-input>

                  <!-- <password-input v-model="form.password" /> -->
                </el-form-item>
                <el-form-item>
                  <el-button
                    style="margin:-20px 0 0 35px;float:right;font-size:14px;color:#818181;"
                    type="text"
                    @click="gotoResetPwdUI"
                  >忘记密码？</el-button>
                </el-form-item>
                <el-form-item>
                  <el-button
                    style="width:270px;height:43px;margin:20px 0 0 0;background:#327cf3;color:#fff"
                    class="full"
                    @click="onSubmit"
                  >立即登录</el-button>
                </el-form-item>
              </el-form>
            </div>
            <div class="adjunctive">
              <div class="adjunctive-nav" @click="wxLogin">
                <img src="../assets/images/wx.png" alt="微信登录" />
                <div>微信登录</div>
              </div>
              <div class="adjunctive-aside" @click="gotoRegisterUI">立即注册</div>
              <!-- <el-button
                style="margin:5px 0 0 105px;color:#373737;font-size:16px"
                type="text"
                @click="gotoRegisterUI"
              >立即注册</el-button>-->
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- <div><customized-footer /></div> -->
  </div>
</template>
<script>
import CustomizedFooter from "components/customized-footer.vue";
import remotejs from "./RemoteJs";
// import CustomizedNav from "components/customized-nav.vue";
// import PasswordInput from "components/password-input.vue";
import { mapMutations } from "vuex";
import { DONE_LOGIN } from "store/mutation-types";
import { watch } from "fs";
import Cookies from "js-cookie";
export default {
  name: "login",
  components: {
    CustomizedFooter,
    remotejs
    // CustomizedNav,
    // PasswordInput
  },
  data() {
    return {
      form: {
        tel: "",
        password: ""
      },
      writeMessageShow: false,
      show: {
        old: false,
        new: false,
        check: false
      },
      rules: {
        tel: [
          {
            required: true,
            message: "请输入登录手机号",
            trigger: ["change", "blur"]
          },
          {
            type: "number",
            message: "手机号必须为数字值",
            trigger: ["change", "blur"]
          },
          {
            pattern: /^[1][356789][0-9]{9}$/,
            message: "请输入正确的手机号",
            trigger: ["change", "blur"]
          }
        ],
        password: [
          {
            required: true,
            message: "请输入登录密码",
            trigger: ["change", "blur"]
          }
          // { pattern:/^(?![0-9]+$)(?![a-zA-Z]+$)[0-9A-Za-z]{6,24}$/,message: '请输入正确密码', trigger: ['change','blur'] },
        ]
      }
    };
  },
  methods: {
    ...mapMutations([DONE_LOGIN]),
    //微信扫码
    wxLogin() {
      this.writeMessageShow = true;
      var aaa = encodeURIComponent(
        "https://www.yinlinkrc.com/business/#/wxlogin"
      );
      window.WxLogin({
        self_redirect: false,
        id: "login_container",
        appid: "wxbca1daaa5765cc51",
        scope: "snsapi_login",
        redirect_uri: aaa,
        state: "asdsfdfgwerwrer",
        style: "black"
      });
    },
    //遮罩层
    writeMessageFun(ev) {
      if (!this.$refs.msk.contains(ev.target)) {
        this.writeMessageShow = false;
      }
    },
    onSubmit() {
      this.$refs["form"].validate(async valid => {
        if (valid) {
          // const devMode = process.env.VUE_APP_DEV_MODE
          // devMode ? `/login?returnUrl=http://${document.location.host}/api/resume/brief` : '/login?returnUrl=/resume/brief'
          // `/login?returnUrl=http://${document.location.host}/api/resume/brief`
          // `/login?returnUrl=/resume/brief`
          this.$_http
            .post(`/business-user/login/phone-pwd`, {
              username: this.form.tel,
              password: this.form.password
            })
            .then(res => {
              if (res.status == 200) {
                let token = res.headers["auth-token"];
                Cookies.set("Btoken", token);
                window.sessionStorage.setItem(
                  "Btoken",
                  res.headers["auth-token"]
                );
                this.state();
                // this.$router.push({ path: "/home" });
              } else {
                return false;
              }
            })
            .catch(error => {
              this.$notify.info({
                title: "消息",
                message: "输入有误，请重新输入"
              });
            });
        } else {
          return false;
        }
      });
    },
    //当前用户信息
    state() {
      this.$http
        .get("/business-core/companyAccounts/user")
        .then(res => {
          if (res.data.code == "200") {
            Cookies.set("status", res.data.data.details.companyId);
            if (res.data.data.details.companyId === 0) {
              this.$router.push({ path: "/enterpriseAudit" });
            } else {
              this.$router.push({ path: "/home" });
            }
          } else {
          }
        })
        .catch(error => {});
    },
    // //公司详情
    // companyDetail() {
    //   this.$http
    //     .get("/business-core/companyes/brief")
    //     .then(res => {
    //       if (res.data.code == "200") {
    //         console.log(res)
    //       } else {
    //       }
    //     })
    //     .catch(error => {
    //       if (error.response.status === 404) {
    //         this.$message({
    //           message: "页面丢失，请重新加载",
    //           type: "error"
    //         });
    //       } else if (error.response.status === 403) {
    //         this.$message({
    //           message: "登陆超时，请重新登录",
    //           type: "error"
    //         });
    //         this.$router.push({ path: "/login" });
    //       } else {
    //         this.$message({
    //           message: error.response.data.message,
    //           type: "error"
    //         });
    //       }
    //     });
    // },
    // gotoHomeUI() {
    //   this.$router.push({ path: "/" });
    // },
    gotoRegisterUI() {
      this.$router.push({ path: "register" });
    },
    gotoResetPwdUI() {
      this.$router.push({ path: "resetpassword" });
    },
    business() {
      window.open("https://www.yinlinkrc.com/client-zh/#/home");
    }
  },
  created() {
    // this.$emit('header', false);
    // this.$emit('footer', false);
  },
  watch: {
    oldsix: function() {
      if (this.oldsix === false) {
        alert(11);
        this.old = false;
      } else {
        this.old = true;
      }
    }
  }
};
</script>


<style lang="stylus">
.containers {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100%;
  background: #327cf3;

  .body {
    .form {
      background: #FFFFFF;
      width: 1176px;
      height: 684px;
      box-shadow: 0px 2px 12px 0px rgba(55, 6, 6, 0.5);
      border-radius: 3px;

      .logo {
        margin: 0 700px 0 50px;
      }

      .photo {
        display: flex;
        flex-direction: row;

        .formlet {
          display: flex;
          flex-direction: column;
          width: 270px;

          .header {
            display: flex;
            flex-direction: row;
            justify-content: space-between;
            margin-bottom: 20px;

            .deng {
              font-family: PingFangSC-Medium;
              color: #327cf3;
              font-size: 24px;
              margin: 39px 0 0 0;
              font-weight: 500;
            }
          }

          .formls {
            .el-input__inner {
              background-color: #f7f7f7;
              border: none;
            }

            .el-input__inner:focus {
              border: 1px solid #CCCCCC;
              background-color: #ffffff;
            }

            .el-form-item.is-error .el-input__inner {
              border-color: #f56c6c;
            }
          }

          .adjunctive {
            cursor: pointer;
            font-family: PingFangSC-Regular;
            color: #373737;
            font-size: 14px;
            display: flex;
            flex-direction: row;
            margin: 0 auto;

            .adjunctive-nav {
              font-family: PingFangSC-Regular;
              color: #848484;
              display: flex;
              flex-direction: row;

              div {
                margin: 0 0 0 5px;
              }
            }

            .adjunctive-aside {
              font-family: PingFangSC-Regular;
              color: #327cf3;
              margin: 0 0 0 30px;
            }
          }
        }
      }
    }
  }
}

.popContainer {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: #323232;
  opacity: 0.9;
  z-index: 1000;

  .messageMaskContent {
    position: relative;
    left: 0;
    width: 50%;
    top: 30;
    margin: auto;
    text-align: center;

    .navs {
      font-family: PingFangSC-Medium;
      color: #FFFFFF;
      font-size: 20px;
      margin: 230px auto 0;
    }

    .imgSrc {
      margin: 30px 0 0 0;

      img {
        width: 284px;
      }
    }

    .foot {
      button {
        background: #222324;
        width: 286px;
        height: 66px;
        font-family: PingFangSC-Medium;
        color: #FFFFFF;
        font-size: 16px;
        border-radius: 33px;
        border: 1px solid #222324;
        padding: 0 47px;
        margin: 30px 0 0 0;
      }
    }
  }
}
</style>