<template>
  <div class="captcha">
    <el-input class="input" type="captcha" v-model="captchaInput" placeholder="验证码"></el-input>
    <el-button class="btn" @click="getCaptcha" :disabled="frozen">{{ captchaStatusText }}</el-button>
  </div>
</template>

<script>
const captchaLabel = "获取验证码";
const countNumber = 60;

export default {
  name: "captchaPass",
  props: ["fromData"],
  data() {
    return {
      frozen: false,
      counter: countNumber,
      captchaInput: "",
      captchaStatusText: captchaLabel
    };
  },
  methods: {
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
      this.$local
        .post("consumer-user/account/phone/vcode", { phone: this.fromData })
        .then(res => {})
        .catch(error => {
          this.$notify.info({
            title: "消息",
            message: error.response.data.message
          });
        });
    }
  },
  watch: {
    captchaInput() {
      this.$emit("input", this.captchaInput);
    }
  }
};
</script>

<style lang="stylus" scoped>
.captcha {
  .input {
    width: 120px;
    height: 43px;
    margin: 0 0 0 25px;
  }

  .btn {
    width: 117px;
    height: 40px;
    margin: 0 0 2px -30px;
    color: #02b9b8;
    border: 1px solid #02b9b8;
  }
}
</style>
