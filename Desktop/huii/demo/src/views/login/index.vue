<template>
  <div class="login-container">
    <h1>智慧服务平台</h1>
    <div class="login-form">
      <h2>欢迎登录</h2>
      <el-form ref="form" :rules="rules" :model="loginForm">
        <el-form-item prop="username">
          <el-input
            v-model.trim="loginForm.username"
            placeholder="请输入账号"
            suffix-icon="el-icon-user-solid"
          ></el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input
            type="password"
            clearable
            v-model.trim="loginForm.password"
            show-password
            placeholder="请输入密码"
            suffix-icon="el-icon-s-goods"
          ></el-input>
        </el-form-item>
        <el-form-item prop="code">
          <div class="verify">
            <el-input
              v-model.trim="loginForm.code"
              placeholder="请输入验证码"
            ></el-input>
            <el-image @click.stop="handleCodeRefresh" :src="codeImageUrl" />
          </div>
        </el-form-item>
        <el-form-item>
          <el-button
            class="login-button"
            :loading="loadingStatus"
            @click="handleVerifyForm"
            type="danger"
            >{{ loadingStatus ? "登录中..." : "立即登录" }}</el-button
          >
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>
<script>
import UserApi from "@/api/user";
import rules from "./rules";
import { mapActions } from "vuex";
export default {
  name: "index",
  data() {
    return {
      loadingStatus: false,
      codeImageUrl: "",
      loginForm: {
        username: "",
        password: "",
        code: "",
        token: "",
      },
      rules,
    };
  },
  methods: {
    async handleGetCaptcha() {
      const { captchaImg, token } = await UserApi.getCaptcha();
      this.codeImageUrl = captchaImg;
      this.loginForm.token = token;
    },
    handleCodeRefresh() {
      this.loginForm.code = "";
      this.handleGetCaptcha();
    },
    handleVerifyForm() {
      this.$refs.form.validate((valid) => {
        if (valid) {
          this.handleSybmitLogin();
        }
      });
    },
    async handleSybmitLogin() {
      try {
        const token = await this.login(this.loginForm);
        if (!token) return;
        this.$notify({ title: "提示", message: "登陆成功", type: "success" });
        this.loadingStatus = true;
        await this.$router.push("/");
      } catch (e) {
        console.log(e);
      } finally {
        this.loadingStatus = false;
      }
    },
    ...mapActions({
        login:'user/login'
    })
  },
  components: {},
  computed: {},
  watch: {},
  created() {
    this.handleGetCaptcha();
  },
};
</script>
<style lang="scss" scoped>
.login-container {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  font-family: Verdana;
  background-size: cover;
  background: url("../../assets copy/images/8531717.jpg");
}
h1 {
  text-align: center;
  font-size: 42px;
  margin-top: 50px;
  color: #fff;
  text-transform: uppercase;
  letter-spacing: 5px;
}
h2 {
  color: #fff;
  font-size: 30px;
  margin-bottom: 30px;
}
.login-form {
  margin: 10% auto 13%;
  width: 20%;
}
.verify {
  width: 100%;
  display: flex;
  align-items: center;
}
.el-image {
  width: 200px;
  height: 40px;
  border-radius: 0px;
  margin-left: 15px;
  cursor: pointer;
}
.login-button {
  width: 100%;
  border-radius: 5px;
}
</style>
