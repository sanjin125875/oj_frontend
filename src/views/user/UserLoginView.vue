<template>
  <div id="userLoginView">
    <a-card class="login-card" :bordered="false">
      <div class="login-header">
        <img class="logo" src="../../assets/oj-logo.svg" alt="logo" />
        <h2 class="title">欢迎回来</h2>
        <p class="subtitle">登录 OJ，开启你的刷题之旅</p>
      </div>
      <a-form
        :model="form"
        layout="vertical"
        size="large"
        @submit="handleSubmit"
      >
        <a-form-item field="userAccount" hide-asterisk>
          <a-input v-model="form.userAccount" placeholder="请输入账号">
            <template #prefix><icon-user /></template>
          </a-input>
        </a-form-item>
        <a-form-item field="userPassword">
          <a-input-password
            v-model="form.userPassword"
            placeholder="请输入密码（不少于 8 位）"
            allow-clear
          >
            <template #prefix><icon-lock /></template>
          </a-input-password>
        </a-form-item>
        <a-form-item>
          <a-button type="primary" html-type="submit" long size="large">
            登 录
          </a-button>
        </a-form-item>
      </a-form>
      <div class="login-footer">
        还没有账号？
        <router-link to="/user/register">立即注册</router-link>
      </div>
    </a-card>
  </div>
</template>

<script setup lang="ts">
import { reactive } from "vue";
import { UserControllerService, UserLoginRequest } from "../../../generated";
import message from "@arco-design/web-vue/es/message";
import { useRouter } from "vue-router";
import { useStore } from "vuex";

/**
 * 表单信息
 */
const form = reactive({
  userAccount: "",
  userPassword: "",
} as UserLoginRequest);

const router = useRouter();
const store = useStore();

/**
 * 提交表单
 * @param data
 */
const handleSubmit = async () => {
  const res = await UserControllerService.userLoginUsingPost(form);
  // 登录成功，跳转到主页
  if (res.code === 0) {
    await store.dispatch("user/getLoginUser");
    message.success("登录成功");
    router.push({
      path: "/",
      replace: true,
    });
  } else {
    message.error("登陆失败，" + res.message);
  }
};
</script>

<style scoped>
#userLoginView {
  display: flex;
  justify-content: center;
  padding: 40px 0;
}

.login-card {
  width: 420px;
  border-radius: 12px;
  box-shadow: 0 8px 30px rgba(22, 93, 255, 0.12);
  padding: 16px 8px;
}

.login-header {
  text-align: center;
  margin-bottom: 28px;
}

.login-header .logo {
  width: 64px;
  height: 64px;
}

.login-header .title {
  margin: 12px 0 4px;
  font-size: 22px;
  font-weight: 600;
  color: #1d2129;
}

.login-header .subtitle {
  margin: 0;
  font-size: 13px;
  color: #86909c;
}

.login-footer {
  text-align: center;
  color: #86909c;
  font-size: 13px;
}

.login-footer a {
  color: #165dff;
  text-decoration: none;
}
</style>
