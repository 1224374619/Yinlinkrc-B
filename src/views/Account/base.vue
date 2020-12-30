<template>
  <div class="container" style="background:#ffffff">
    <div class="popContainer" v-show="writeMessageShow" @click="writeMessageFun($event)">
      <div class="messageMaskContent" ref="msk">
        <div class="navs">微信登录</div>
        <!-- <div >
          <img src="../assets/images/foot-wxs.png" />
        </div>-->
        <remotejs src="https://res.wx.qq.com/connect/zh_CN/htmledition/js/wxLogin.js" />
        <div class="imgSrc" id="login_container"></div>
        <!-- <div class="foot">
          <button>
            请使用微信扫描二维码登录
            “银领人才网”
          </button>
        </div>-->
      </div>
    </div>
    <!-- <el-dialog title="更换头像" :visible.sync="dialogVisible" width="30%" :before-close="handleClose">
      <div style="margin:-20px 0 0 0">上传头像：</div>
      <div style="margin: 20px auto;width:100%">
        <el-upload
          class="avatar-uploaders"
          :action="uploadCompanyFile"
          :data="uploadDatas"
          :headers="myHeaders"
          :show-file-list="false"
          :on-success="handleAvatarSuccess"
          :before-upload="beforeAvatarUpload"
          :on-error="handleAvatarError"
        >
          <i class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="dialogVisible = false">确 定</el-button>
      </span>
    </el-dialog>-->
    <el-dialog title :visible.sync="wxdialogVisible" width="25%" :show-close="false">
      <div class="dialogTitle">
        <span>手机号</span>
        <span>182****7638</span>
      </div>
      <div class="dialogCode">
        <span>验证码</span>
        <span>
          <el-input style="width:150px;margin:0 20px 0 10px" placeholder="请输入验证码" v-model="wxCode"></el-input>
          <el-button @click="getCaptcha" :disabled="frozen">{{ captchaStatusText }}</el-button>
        </span>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button plain @click="wxdialogVisible = false">取 消</el-button>
        <el-button style="margin:0 0 0 50px" type="primary" @click="Unbund()">绑 定</el-button>
      </span>
    </el-dialog>
    <el-dialog title="更换手机号" :visible.sync="dialogVisiblephone" width="30%">
      <el-form
        :model="ruleForm"
        :rules="rules"
        ref="ruleForm"
        label-width="100px"
        class="demo-ruleForm"
      >
        <el-form-item label="手机号" prop="phone">
          <el-input style="width:240px" placeholder="请输入手机号" v-model="ruleForm.phone"></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="phone">
          <el-input style="width:240px" placeholder="请输入原密码" v-model="ruleForm.password"></el-input>
        </el-form-item>
        <el-form-item label="验证码" prop="vcode">
          <el-input
            style="width:240px;margin:0 20px 0 0"
            placeholder="请输入验证码"
            v-model="ruleForm.vcode"
          ></el-input>
          <el-button class="btn" @click="getCaptcha" :disabled="frozen">{{ captchaStatusText }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button @click="dialogVisiblephone = false">取消</el-button>
          <el-button type="primary" @click="updatePhone('ruleForm')">确定</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>
    <el-dialog title="更换邮箱" :visible.sync="dialogVisibleemail" width="30%">
      <el-form
        :model="ruleForms"
        :rules="rule"
        ref="ruleForms"
        label-width="100px"
        class="demo-ruleForm"
      >
        <el-form-item label="邮箱账号" prop="email">
          <el-input style="width:240px" v-model="ruleForms.email"></el-input>
        </el-form-item>
        <el-form-item label="验证码" prop="vcode">
          <el-input
            style="width:240px;margin:0 20px 0 0"
            placeholder="请输入验证码"
            v-model="ruleForms.vcode"
          ></el-input>
          <el-button class="btn" @click="getCaptchas" :disabled="frozen">{{ captchaStatusText }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button @click="dialogVisibleemail = false">取消</el-button>
          <el-button type="primary" @click="updateEmail('ruleForms')">确定</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>
    <div class="nav">
      <span style="margin:0 0 0 20px">账号管理</span>
    </div>
    <div class="aside">
      <div>
        <el-upload
          class="avatar-uploader"
          :action="uploadCompanyFile"
          :data="uploadDatas"
          :headers="myHeaders"
          :show-file-list="false"
          :on-success="handleAvatarSuccess"
          :on-error="handleAvatarError"
        >
          <img v-if="this.imageUrl" :src="imageUrl" class="avatar" />
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>
        <!-- <div @click="avatar" style="font-size:14px;color:#ff7152;margin:10px 0 0 65px">更换头像</div> -->
      </div>
      <div class="right">
        <div style="text-align:right;margin:0 0 0 30px">
          <!-- <div>账号：</div> -->
          <div>姓名：</div>
          <div>角色：</div>
          <!-- <div>所属组织：</div> -->
          <div>手机号：</div>
          <div>邮箱：</div>
          <div style="display: flex;flex-direction: row;">
            <img src="../../assets/images/wx.png" style="width:20px;" />
            <span style="margin:0 0 0 5px">微信绑定：</span>
          </div>
        </div>
        <div>
          <!-- <div>{{formDate.phone}}</div> -->
          <div>{{formDate.realName}}</div>
          <div>{{formDate.role}}</div>
          <!-- <div>{{formDate.role}}</div> -->
          <div>
            {{formDate.phone}}
            <span
              @click="dialogVisiblephone = true"
              style="font-size:14px;color:#ff7152;line-height:16px"
            >更换绑定手机号</span>
          </div>
          <div>
            {{formDate.email}}
            <span
              @click="dialogVisibleemail = true"
              style="font-size:14px;color:#ff7152;line-height:16px"
            >更换绑定邮箱</span>
          </div>
          <div class="elbutton">
            <div style="padding:0 0 30px 0" v-if="this.wxState">
              <span>二月</span>
              <span style="font-size:16px;color:#ff7152;line-height:16px;margin:0 0 0 50px">解绑</span>
            </div>
            <div style="margin:-33px 0 0 0" v-else>
              <el-button class="pributton" @click="wxLogin" plain>立即绑定</el-button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Cookies from "js-cookie";
import remotejs from "../RemoteJs";
let token = Cookies.get("Btoken");
const captchaLabel = "获取验证码";
const countNumber = 60;
export default {
  components: {
    remotejs
  },
  data() {
    // 手机号验证
    var checkPhone = (rule, value, callback) => {
      const phoneReg = /^1[3|4|5|6|7|8][0-9]{9}$/;
      if (!value) {
        return callback(new Error("电话号码不能为空"));
      }
      setTimeout(() => {
        if (!Number.isInteger(+value)) {
          callback(new Error("请输入数字值"));
        } else {
          if (phoneReg.test(value)) {
            callback();
          } else {
            callback(new Error("电话号码格式不正确"));
          }
        }
      }, 100);
    };
    return {
      redirectUri: encodeURIComponent(
        "https://www.yinlinkrc.com/business/#/account/base"
      ),
      wxCode: "",
      myHeaders: { "Auth-Token": token },
      uploadDatas: {
        label: "company-account-avatar"
      },
      thirdPartyId: "",
      writeMessageShow: false,
      wxState: true,
      code: "",
      state: "",
      frozen: false,
      phoneOne: "",
      nickname: "",
      counter: countNumber,
      captchaInput: "",
      captchaStatusText: captchaLabel,

      wxdialogVisible: false,
      dialogVisible: false,
      dialogVisiblephone: false,
      dialogVisibleemail: false,
      ruleForm: {
        phone: "",
        vcode: "",
        password: ""
      },
      ruleForms: {
        email: ""
      },
      imageUrl: "",
      file: "",
      rules: {
        vcode: [{ required: true, message: "请输入验证码", trigger: "blur" }],
        password: [{ required: true, message: "请输入密码", trigger: "blur" }],
        phone: [{ required: true, validator: checkPhone, trigger: "blur" }],
        email: [
          { required: true, message: "请填写邮箱", trigger: "change" },
          {
            pattern: /^([a-zA-Z0-9_.-])+@(([a-zA-Z0-9-])+\.)+(com|cn|net|com.cn|com.tw|com.hk)$/,
            message: "邮箱格式错误",
            trigger: ["change", "blur"]
          }
        ]
      },
      rule: {
        email: [
          { required: true, message: "请填写邮箱", trigger: "change" },
          {
            pattern: /^([a-zA-Z0-9_.-])+@(([a-zA-Z0-9-])+\.)+(com|cn|net|com.cn|com.tw|com.hk)$/,
            message: "邮箱格式错误",
            trigger: ["change", "blur"]
          }
        ],
        vcode: [{ required: true, message: "请输入验证码", trigger: "blur" }]
      },
      formDate: {}
    };
  },
  watch: {
    $route(to, from) {
      console.log(to);
      let url = window.location.href;
      if (url.indexOf("?") != -1) {
        var str = url.substr(1);
        var strs = str.split("=");
        this.code = strs[1].split("&")[0];
        this.state = strs[2];
      }
      console.log(this.code, this.state);
    }
  },
  methods: {
    //微信扫码
    wxLogin() {
      this.writeMessageShow = true;
      window.WxLogin({
        self_redirect: false,
        id: "login_container",
        appid: "wxbca1daaa5765cc51",
        scope: "snsapi_login",
        redirect_uri: this.redirectUri,
        state: "asdsfdfgwerwrer",
        style: "black",
        href:
          "data:text/css;base64,LmltcG93ZXJCb3ggLnN0YXR1cyB7CiAgbWFyZ2luOiAxMHB4IDAgNjBweCAwOwogIGJhY2tncm91bmQtY29sb3I6ICNmZmZmZmY7CiAgLXdlYmtpdC1ib3JkZXItcmFkaXVzOiAxMDBweDsKICB6LWluZGV4OiA2MDA7CiAgLXdlYmtpdC1ib3gtc2hhZG93OiBpbnNldCAwIDVweCAxMHB4IC01cHggIzE5MTkxOSwgMCAxcHggMCAwICM0NDQ7CiAgYm94LXNoYWRvdzogaW5zZXQgMCA1cHggMTBweCAtNXB4ICMxOTE5MTksIDAgMXB4IDAgMCAjNDQ0Owp9"
      });
    },
    //绑定详情
    bindWechat() {
      this.$http
        .get("business-user/binding/wechat")
        .then(res => {
          this.wxState = true;
          this.thirdPartyId = res.data.data.thirdPartyId;
          this.nickname = res.data.data.nickname;
          this.phoneOne = res.data.data.phone;
        })
        .catch(error => {
          this.wxState = false;
        });
    },
    //解绑
    Unbund() {
      this.$http
        .delete(`business-user/binding/${this.thirdPartyId}`)
        .then(res => {
          this.wxState = false;
        })
        .catch(error => {});
    },
    //绑定
    bund() {
      let params = {
        code: this.code,
        phone: this.phoneOne,
        scode: this.wxCode,
        state: this.state
      };
      this.$http
        .post("business-user/binding/wechat", params)
        .then(res => {
          this.wxState = true;
          this.bindWechat();
        })
        .catch(error => {});
    },
    //验证码
    getCaptcha() {
      this.frozen = true;
      const handler = setInterval(() => {
        this.captchaStatusText = `${captchaLabel}(${--this.counter}s)`;
        if (this.counter === 0) {
          clearInterval(handler);
          this.counter = countNumber;
          this.captchaStatusText = captchaLabel;
          this.frozen = false;
        }
      }, 1000);
      this.$locals.post("/business-user/account/phone/vcode", {
        phone: this.ruleForm.phone
      });
    },
    getCaptchas() {
      this.frozen = true;
      const handler = setInterval(() => {
        this.captchaStatusText = `${captchaLabel}(${--this.counter}s)`;
        if (this.counter === 0) {
          clearInterval(handler);
          this.counter = countNumber;
          this.captchaStatusText = captchaLabel;
          this.frozen = false;
        }
      }, 1000);
      this.$locals
        .post("/business-user/account/phone/vcode", {
          phone: this.formDate.phone
        })
        .catch(error => {
          if (error.response.status === 404) {
            this.$notify.info({
              title: "消息",
              message: "页面丢失，请重新加载"
            });
          } else if (error.response.status === 403) {
            this.$notify.info({
              title: "消息",
              message: "登陆超时，请重新登录"
            });
          } else {
            this.$notify.info({
              title: "消息",
              message: error.response.data.message
            });
          }
        });
    },
    //修改手机号
    updatePhone() {
      let params = {
        password: this.ruleForm.password,
        phone: this.ruleForm.phone,
        vcode: this.ruleForm.vcode
      };
      this.$http
        .put("/business-user/account/phone", params)
        .then(res => {
          if (res.data.code == "200") {
            this.dialogVisiblephone = false;
            this.base();
          } else {
          }
        })
        .catch(error => {});
    },
    //修改邮箱
    updateEmail() {
      let params = {
        email: this.ruleForms.email,
        vcode: this.ruleForms.vcode
      };
      this.$http
        .put("/business-user/account/email", params)
        .then(res => {
          if (res.data.code == "200") {
            this.dialogVisibleemail = false;
            this.base();
          } else {
          }
        })
        .catch(error => {});
    },
    //图片上传
    handleAvatarSuccess(res, file) {
      this.file = res.data;
      console.log(URL.createObjectURL(file.raw));
      this.imageUrl = URL.createObjectURL(file.raw);
      this.avatar();
    },
    handleAvatarError(err, file, fileList) {
      this.$notify.info({
        title: "消息",
        message: "图片上传失败，请重新上传"
      });
    },
    //获取用户得基本信息
    base() {
      this.$http
        .get("/business-user/account")
        .then(res => {
          if (res.data.code == "200") {
            this.formDate = res.data.data;
            this.imageUrl = res.data.data.avatarUrl;
          } else {
          }
        })
        .catch(error => {});
    },
    avatar() {
      let params = this.file;
      this.$http
        .post("/business-user/account/avatar", params)
        .then(res => {
          if (res.data.code == "200") {
          } else {
          }
        })
        .catch(error => {});
    }
  },
  created() {
    let token = Cookies.get("Btoken");
    if (token) {
      this.base();
    } else {
      this.$notify.info({
        title: "消息",
        message: "登陆超时，请重新登录"
      });
      this.$router.push({ path: "/login" });
    }
    this.bindWechat();
  },
  computed: {
    uploadUrl() {
      return "/api/v3/business-user/account/avatar";
    },
    uploadCompanyFile() {
      return "/api/3/file-service/files/upload";
    }
  }
};
</script>

<style lang="stylus" >
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

    .nav {
      font-family: PingFangSC-Medium;
      color: #FFFFFF;
      font-size: 20px;
      margin: 130px 0 0 0;
    }

    .imgSrc {
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
        margin: 10px 0 0 0;
      }
    }
  }
}

.dialogTitle {
  font-family: PingFangSC-Regular;
  color: #373737;
  font-size: 16px;
  text-align: left;
  margin: 0 0 0 55px;

  span:nth-child(2) {
    margin: 0 0 0 5px;
  }
}

.el-dialog__footer {
  text-align: center;
  padding: 20px 0 60px 0;
}

.dialogCode {
  display: flex;
  flex-direction: row;
  margin: 20px 0 0 55px;
  font-family: PingFangSC-Regular;
  color: #373737;
  font-size: 16px;
}

.nav {
  margin: 30px 0 0 0;
  width: 960px;
  height: 50px;
  background: #F1F1F1;
  font-family: PingFangSC-Medium;
  color: #327cf3;
  line-height: 50px;
  font-size: 16px;
  border: 1px solid rgba(244, 244, 244, 1);
  box-shadow: 0px 2px 10px 0px rgba(245, 245, 245, 1);
}

.aside {
  display: flex;
  flex-direction: row;
  width: 960px;
  border: 1px solid red;
  height: auto;
  background: #ffffff;
  margin: 20px 0 0 0;
  border: 1px solid rgba(244, 244, 244, 1);
  box-shadow: 0px 2px 10px 0px rgba(245, 245, 245, 1);

  .right {
    display: flex;
    flex-direction: row;
    margin: -20px 0 100px 0;

    div {
      padding: 30px 0 0 0;
    }

    .elbutton {
      .el-button {
        width: 94px;
        height: 30px;
        border: 0.75px solid #327cf3;
        border-radius: 5px;
        font-family: PingFangSC-Regular;
        color: #327cf3;
        font-size: 16px;
        line-height: 0px;
        padding: 0 1px 0 0;
      }
    }
  }
}
</style>
<style>
.avatar-uploader .el-upload {
  border: 1px dashed #d9d9d9;
  border-radius: 50px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
  margin: 30px 0 0 40px;
}
.avatar-uploaders .el-upload {
  border: 1px dashed #d9d9d9;
  border-radius: 50px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
  margin: 0 0 0 230px;
}
.avatar-uploader .el-upload:hover {
  border-color: #02b9b8;
}
.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 100px;
  height: 100px;
  line-height: 100px;
  text-align: center;
}
.avatar {
  width: 100px;
  height: 100px;
  display: block;
}
</style>