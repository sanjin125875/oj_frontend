<template>
  <div id="userRegisterView">
    <a-card class="register-card" :bordered="false">
      <div class="register-header">
        <img class="logo" src="../../assets/oj-logo.svg" alt="logo" />
        <h2 class="title">创建账号</h2>
        <p class="subtitle">注册 OJ，和我们一起进步</p>
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
        <a-form-item field="checkPassword">
          <a-input-password
            v-model="form.checkPassword"
            placeholder="请确认密码"
            allow-clear
          >
            <template #prefix><icon-safe /></template>
          </a-input-password>
        </a-form-item>
        <a-form-item>
          <a-button type="primary" html-type="submit" long size="large">
            注 册
          </a-button>
        </a-form-item>
      </a-form>
      <div class="register-footer">
        已有账号？
        <router-link to="/user/login">去登录</router-link>
      </div>
    </a-card>
  </div>
</template>

<script setup lang="ts">
import { reactive } from "vue";
import { IconSafe, IconUser } from "@arco-design/web-vue/es/icon";
import { UserControllerService, UserRegisterRequest } from "../../../generated";
import message from "@arco-design/web-vue/es/message";
import { useRouter } from "vue-router";

/**
 * 表单信息
 */
const form = reactive({
  userAccount: "",
  userPassword: "",
  checkPassword: "",
} as UserRegisterRequest);

const router = useRouter();

/**
 * 提交表单
 */
const handleSubmit = async () => {
  if (form.userPassword !== form.checkPassword) {
    message.error("两次输入的密码不一致");
    return;
  }
  try {
    const res = await UserControllerService.userRegisterUsingPost(form);
    if (res.code === 0) {
      message.success("注册成功，请登录");
      router.push({
        path: "/user/login",
        replace: true,
      });
    } else {
      message.error("注册失败，" + res.message);
    }
  } catch (e: any) {
    message.error("注册失败，请检查后端服务是否启动：" + (e?.message ?? e));
  }
};
</script>

<style scoped>
#userRegisterView {
  display: flex;
  justify-content: center;
  padding: 40px 0;
}

.register-card {
  width: 420px;
  border-radius: 12px;
  box-shadow: 0 8px 30px rgba(22, 93, 255, 0.12);
  padding: 16px 8px;
}

.register-header {
  text-align: center;
  margin-bottom: 28px;
}

.register-header .logo {
  width: 64px;
  height: 64px;
}

.register-header .title {
  margin: 12px 0 4px;
  font-size: 22px;
  font-weight: 600;
  color: #1d2129;
}

.register-header .subtitle {
  margin: 0;
  font-size: 13px;
  color: #86909c;
}

.register-footer {
  text-align: center;
  color: #86909c;
  font-size: 13px;
}

.register-footer a {
  color: #165dff;
  text-decoration: none;
}
</style>
