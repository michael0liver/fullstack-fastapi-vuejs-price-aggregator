<template>
  <div class="login-container">
    <el-form
      ref="loginForm"
      :model="loginForm"
      :rules="loginRules"
      class="login-form"
      autocomplete="on"
      label-position="left"
    >
      <div class="title-container">
        <h3 class="title">
          Please login to continue
        </h3>
      </div>

      <el-form-item prop="username">
        <span class="svg-container">
          <svg-icon name="user" />
        </span>
        <el-input
          ref="username"
          v-model="loginForm.username"
          :placeholder="Username"
          name="username"
          type="text"
          tabindex="1"
          autocomplete="on"
        />
      </el-form-item>

      <el-tooltip
        v-model="capsTooltip"
        content="Caps lock is On"
        placement="right"
        manual
      >
        <el-form-item prop="password">
          <span class="svg-container">
            <svg-icon name="password" />
          </span>
          <el-input
            :key="passwordType"
            ref="password"
            v-model="loginForm.password"
            :placeholder="Password"
            :type="passwordType"
            name="password"
            tabindex="2"
            autocomplete="on"
            @keyup.native="checkCapslock"
            @blur="capsTooltip = false"
            @keyup.enter.native="handleLogin"
          />
          <span
            class="show-pwd"
            @click="showPwd"
          >
            <svg-icon
              :name="passwordType === 'password' ? 'eye-off' : 'eye-on'"
            />
          </span>
        </el-form-item>
      </el-tooltip>

      <el-button
        :loading="loading"
        type="primary"
        style="width:100%; margin-bottom:30px;"
        @click.native.prevent="handleLogin"
      >
        Login
      </el-button>

      <div style="position:relative">
        <div class="tips">
          <el-link
            type="primary"
            href="https://www.google.com"
            target="_blank"
          >
            Sign Up
          </el-link>
        </div>
        <div class="tips">
          <el-link
            type="primary"
            href="https://www.google.com"
            target="_blank"
          >
            Forgot Password?
          </el-link>
        </div>
      </div>
    </el-form>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Watch } from 'vue-property-decorator';
import { Route } from 'vue-router';
import { Dictionary } from 'vue-router/types/router';
import { Form as ElForm, Input } from 'element-ui';
import { UserMeModule } from '@/store/modules/me';
import { isValidUsername } from '@/utils/validate';
import { IUserUpdate } from '@/api/types';

@Component({
  name: 'Register',
  components: {}
})
export default class extends Vue {

  private registerForm = {

  };

  private loginForm = {
    username: 'mo175@live.com',
    password: 'password'
  };

  private loginRules = {
    username: [{ trigger: 'blur', required: true, message: 'requird field m8' }],
    password: [{ trigger: 'blur' }]
  };

  private passwordType = 'password';
  private loading = false;
  private capsTooltip = false;
  private redirect?: string;
  private otherQuery: Dictionary<string> = {};

  mounted() {
    if (this.loginForm.username === '') {
      (this.$refs.username as Input).focus();
    } else if (this.loginForm.password === '') {
      (this.$refs.password as Input).focus();
    }
  }

  // private submit() {
  //   const updateData: IUserUpdate = {};
  //   if (this.password !== '') {
  //     updateData.password = this.password;
  //   }
  //   /*
  //   Does API request to update current user with data currently inside component.
  //   on error will loop through details and create message toast.
  //
  //    */
  //   UserMeModule.UpdateUserMe(this.user).then(() => {
  //     this.$message({
  //       message: 'User information has been updated successfully',
  //       type: 'success',
  //       duration: 5 * 1000
  //     });
  //   }).catch((rejects) => {
  //     const { detail } = rejects.response.data;
  //     detail.map((detail: { msg: any }) =>
  //       this.$message({
  //         message: detail.msg,
  //         type: 'error',
  //         duration: 5 * 1000
  //       }));
  //   });
  // };

  private handleLogin() {
    (this.$refs.loginForm as ElForm).validate(async(valid: boolean) => {
      if (valid) {
        this.loading = true;
        await UserMeModule.Login(this.loginForm);
        await this.$router.push({
          path: this.redirect || '/',
          query: this.otherQuery
        });
      } else {
        return false;
      }
    });
  }

}
</script>

<style lang="scss">
// References: https://www.zhangxinxu.com/wordpress/2018/01/css-caret-color-first-line/
@supports (-webkit-mask: none) and (not (cater-color: $loginCursorColor)) {
  .login-container .el-input {
    input {
      color: $loginCursorColor;
    }
    input::first-line {
      color: $lightGray;
    }
  }
}

.login-container {
  .el-input {
    display: inline-block;
    height: 47px;
    width: 85%;

    input {
      height: 47px;
      background: transparent;
      border: 0px;
      border-radius: 0px;
      padding: 12px 5px 12px 15px;
      color: $lightGray;
      caret-color: $loginCursorColor;
      -webkit-appearance: none;

      &:-webkit-autofill {
        box-shadow: 0 0 0px 1000px $loginBg inset !important;
        -webkit-text-fill-color: #fff !important;
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
.login-container {
  height: 100%;
  width: 100%;
  overflow: hidden;
  background-color: $loginBg;

  .login-form {
    position: relative;
    width: 520px;
    max-width: 100%;
    padding: 160px 35px 0;
    margin: 0 auto;
    overflow: hidden;
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
    color: $darkGray;
    vertical-align: middle;
    width: 30px;
    display: inline-block;
  }

  .title-container {
    position: relative;

    .title {
      font-size: 26px;
      color: $lightGray;
      margin: 0px auto 40px auto;
      text-align: center;
      font-weight: bold;
    }
  }

  .show-pwd {
    position: absolute;
    right: 10px;
    top: 7px;
    font-size: 16px;
    color: $darkGray;
    cursor: pointer;
    user-select: none;
  }
}
</style>
