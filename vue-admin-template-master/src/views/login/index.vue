<template>
  <div class="login-container">
    <div class="title-container">
      <h3 class="title">河北省农村乱占耕地建房专项整治系统平台</h3>
    </div>

    <div class="alertD">
      <div style="text-align: center" class="content">
        <i class="el-icon-close" @click="closeAPP"></i>
        <div><span style="color: red">当前app仅支持安卓手机</span></div>
        <div>
          打开手机浏览器输入http://110.249.159.46:8099/download/app-release.apk
        </div>
        <div>
          或者使用手机浏览器扫描下方二维码(<span style="color: red"
            >不支持直接使用微信扫码</span
          >)
        </div>
        <div class="imgBox">
          <img
            src="@/assets/ncgdjfAppSJZ.png"
            width="100"
            height="100"
            alt=""
          />
        </div>
        <div>
          或者先<a
            style="color: blue"
            href="http://110.249.159.46:8099/download/app-release.apk"
            >下载到电脑端</a
          >再发送到手机进行安装
        </div>
      </div>
    </div>

    <el-form
      ref="loginForm"
      :model="loginForm"
      :rules="loginRules"
      class="login-form"
      auto-complete="on"
      label-position="left"
    >
      <el-form-item prop="username">
        <span class="svg-container">
          <svg-icon icon-class="user" />
        </span>
        <el-input
          ref="username"
          v-model="loginForm.username"
          placeholder="请输入注册时的手机号"
          name="username"
          type="text"
          tabindex="1"
          auto-complete="on"
        />
      </el-form-item>

      <el-form-item prop="password">
        <span class="svg-container">
          <svg-icon icon-class="password" />
        </span>
        <el-input
          :key="passwordType"
          ref="password"
          v-model="loginForm.password"
          :type="passwordType"
          placeholder="请输入密码"
          name="password"
          tabindex="2"
          auto-complete="on"
          @keyup.enter.native="handleLogin"
        />
        <span class="show-pwd" @click="showPwd">
          <svg-icon
            :icon-class="passwordType === 'password' ? 'eye' : 'eye-open'"
          />
        </span>
      </el-form-item>

      <el-button
        :loading="loading"
        type="primary"
        style="width: 100%; margin-bottom: 30px"
        @click.native.prevent="handleLogin"
        >登录</el-button
      >

      <div class="tips">
        <span style="margin-right: 150px; cursor: pointer" @click="loadAPP"
          >手机端下载</span
        >
        <span style="cursor: pointer"> 忘记密码</span>
      </div>
    </el-form>
  </div>
</template>

<script>
import { validUsername } from "@/utils/validate";
import $ from "jquery";

export default {
  name: "Login",
  data() {
    const validateUsername = (rule, value, callback) => {
      if (!validUsername(value)) {
        callback(new Error("请输入正确的手机号"));
      } else {
        callback();
      }
    };
    const validatePassword = (rule, value, callback) => {
      if (value.length < 1) {
        callback(new Error("请输入密码"));
      } else {
        callback();
      }
    };
    return {
      loginForm: {
        username: "admin",
        password: "111111",
      },
      loginRules: {
        username: [
          { required: true, trigger: "blur", validator: validateUsername },
        ],
        password: [
          { required: true, trigger: "blur", validator: validatePassword },
        ],
      },
      loading: false,
      passwordType: "password",
      redirect: undefined,
    };
  },
  watch: {
    $route: {
      handler: function (route) {
        this.redirect = route.query && route.query.redirect;
      },
      immediate: true,
    },
  },
  methods: {
    showPwd() {
      if (this.passwordType === "password") {
        this.passwordType = "";
      } else {
        this.passwordType = "password";
      }
      this.$nextTick(() => {
        this.$refs.password.focus();
      });
    },
    loadAPP() {
      $(".alertD").css("display", "block");
    },
    closeAPP() {
      $(".alertD").css("display", "none");
    },
    handleLogin() {
      this.$refs.loginForm.validate((valid) => {
        if (valid) {
          this.loading = true;
          this.$store
            .dispatch("user/login", this.loginForm)
            .then(() => {
              this.$router.push({ path: this.redirect || "/" });
              this.loading = false;
            })
            .catch(() => {
              this.loading = false;
            });
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
  },
};
</script>

<style lang="scss">
/* 修复input 背景不协调 和光标变色 */
/* Detail see https://github.com/PanJiaChen/vue-element-admin/pull/927 */

$bg: #283443;
$light_gray: #fff;
$cursor: #fff;

@supports (-webkit-mask: none) and (not (cater-color: $cursor)) {
  .login-container .el-input input {
    color: $cursor;
  }
}

/* reset element-ui css */
.login-container {
  .el-input {
    display: inline-block;
    height: 47px;
    width: 85%;

    input {
      background: transparent;
      border: 0px;
      -webkit-appearance: none;
      border-radius: 0px;
      padding: 12px 5px 12px 15px;
      color: $light_gray;
      height: 47px;
      caret-color: $cursor;

      &:-webkit-autofill {
        box-shadow: 0 0 0px 1000px $bg inset !important;
        -webkit-text-fill-color: $cursor !important;
      }
    }
  }

  .el-form-item {
    border: 1px solid rgba(255, 255, 255, 0.1);
    background: rgba(0, 0, 0, 0.1);
    border-radius: 5px;
    color: #454545;
  }
}
</style>

<style lang="scss" scoped>
$bg: #2d3a4b;
$dark_gray: #889aa4;
$light_gray: #eee;

.login-container {
  min-height: 100%;
  width: 100%;
  background: url(../../assets/bgc-1.jpg) no-repeat;
  background-position: center;
  height: 100%;
  width: 100%;
  background-size: cover;
  position: relative;
  overflow: hidden;
  color: #fff;

  .login-form {
    position: absolute;
    top: 150px;
    right: 260px;
    width: 400px;
    height: 400px;
    max-width: 100%;
    background: url(../../assets/login-box.png) no-repeat;
    background-size: 100%;
    padding: 90px 60px 0;
    margin: 0 auto;
    overflow: hidden;

    .svg-container {
      color: #fff;
    }

    .el-form-item {
      margin-bottom: 15px;

      .show-pwd {
        color: #fff;
      }
    }
  }

  .tips {
    font-size: 14px;
    color: #fff;
    margin-bottom: 10px;

    span {
      &:first-of-type {
        margin-right: 16px;
      }
    }
  }

  .svg-container {
    padding: 6px 5px 6px 15px;
    color: $dark_gray;
    vertical-align: middle;
    width: 30px;
    display: inline-block;
  }

  .title-container {
    // position: relative;

    .title {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 50px;
      line-height: 50px;
      text-align: center;
      background-color: rgba(40, 66, 151, 0.3);
      color: #fff;
      font-size: 26px;
      letter-spacing: 10px;
      font-weight: 500;
      margin: 0;
    }
  }

  .alertD {
    position: absolute;
    background-color: rgba(0, 0, 0, 0.4);
    width: 100%;
    height: 100%;
    display: none;

    .content {
      position: absolute;
      background-color: #fff;
      top: 30%;
      left: 30%;
      color: #000;
      padding: 50px;
      z-index: 99;
      line-height: 30px;
      border-radius: 8px;

      .el-icon-close {
        position: absolute;
        top: 10px;
        right: 10px;
        font-size: 25px;
      }
    }
  }

  .show-pwd {
    position: absolute;
    right: 10px;
    top: 7px;
    font-size: 16px;
    color: $dark_gray;
    cursor: pointer;
    user-select: none;
  }
}
</style>
