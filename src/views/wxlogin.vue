<template>
<div></div>
</template>

<script>
import Cookies from "js-cookie";
export default {
  data() {
    return {};
  },
  methods: {
    wxlogin() {
      let url = window.location.href;
      console.log(url)
      if (url.indexOf("?") != -1) {
        var str = url.substr(1);
        var strs = str.split("=");
      }
      console.log(str, strs);
      let params = {
        code: strs[1].split("&")[0],
        state: strs[2]
      };
      this.$localo
        .get("/business-user/wechat/account/login", { params: params })
        .then(res => {
          let obj = JSON.stringify(res.data.data.accessTokenVO);
          console.log(encodeURIComponent(obj));
          console.log(res)
          if (res.data.code == "WE_CHAT_IS_NOT_BOUND") {
            this.$router.push({
              path: "/wxbind",
              query: {
                wxlogin: encodeURIComponent(obj)
              }
            });
          } else {
            console.log('111111')
            let token = res.headers["auth-token"];
            console.log(token)
            Cookies.set("token", Btoken);
            this.$router.push({ path: "/home" });
            console.log('2222222222')
          }
        })
        .catch(error => {});
    }
    // //获取简历简讯
    // brief() {
    //   this.$http
    //     .get("/consumer-core/resume/brief")
    //     .then(res => {
    //       if (res.data.data.base !== null) {
    //         this.$store.state.avatarUrl = res.data.data.base.avatarUrl;
    //       } else {
    //       }
    //       this.$router.push({ path: "/home" });
    //     })
    //     .catch(error => {});
    // }
  },
  created() {
    this.wxlogin();
  }
};
</script>
<style lang="stylus"></style>