<template>
  <div class="no-auth-view">
    <a-result status="403" subtitle="抱歉，你没有权限访问该页面">
      <template #extra>
        <a-space>
          <a-button type="primary" @click="goHome">返回首页</a-button>
          <a-button @click="goBack">返回上一页</a-button>
        </a-space>
      </template>
    </a-result>
  </div>
</template>

<script setup lang="ts">
import { useRouter } from "vue-router";

const router = useRouter();

const goHome = () => {
  router.push({ path: "/" });
};

const goBack = () => {
  router.back();
};
</script>

<style scoped>
.no-auth-view {
  display: flex;
  justify-content: center;
  padding: 80px 0;
}
</style>
