<template>
  <div id="adminView">
    <div class="banner">
      <h2>管理员控制台</h2>
      <p>欢迎回来，{{ userName }}，祝你今天工作顺利</p>
    </div>
    <a-row :gutter="16">
      <a-col :span="6" v-for="item in stats" :key="item.title">
        <a-card class="stat-card" :bordered="false">
          <div class="stat-item">
            <div class="stat-icon" :style="{ background: item.color }">
              <component :is="item.icon" />
            </div>
            <div>
              <div class="stat-value">{{ item.value }}</div>
              <div class="stat-title">{{ item.title }}</div>
            </div>
          </div>
        </a-card>
      </a-col>
    </a-row>
    <a-card class="quick-card" :bordered="false" title="快捷操作">
      <a-space size="medium">
        <a-button type="primary" @click="router.push('/add/question')">
          创建题目
        </a-button>
        <a-button @click="router.push('/manage/question')">管理题目</a-button>
        <a-button @click="router.push('/about')">关于系统</a-button>
      </a-space>
    </a-card>
  </div>
</template>

<script setup lang="ts">
import { computed } from "vue";
import { useRouter } from "vue-router";
import { useStore } from "vuex";
import {
  IconUser,
  IconFile,
  IconSend,
  IconCheckCircle,
} from "@arco-design/web-vue/es/icon";

const router = useRouter();
const store = useStore();

const userName = computed(
  () => store.state.user?.loginUser?.userName ?? "管理员"
);

const stats = [
  { title: "用户总数", value: "128", icon: IconUser, color: "#165dff" },
  { title: "题目总数", value: "56", icon: IconFile, color: "#00b42a" },
  { title: "提交总数", value: "1024", icon: IconSend, color: "#ff7d00" },
  { title: "通过率", value: "68%", icon: IconCheckCircle, color: "#722ed1" },
];
</script>

<style scoped>
#adminView {
  max-width: 1200px;
  margin: 0 auto;
}

.banner {
  background: linear-gradient(120deg, #165dff, #6aa1ff);
  border-radius: 12px;
  color: #fff;
  padding: 32px;
  margin-bottom: 16px;
}

.banner h2 {
  margin: 0 0 8px;
  font-size: 22px;
}

.banner p {
  margin: 0;
  opacity: 0.85;
}

.stat-card {
  border-radius: 12px;
  margin-bottom: 16px;
}

.stat-item {
  display: flex;
  align-items: center;
  gap: 16px;
}

.stat-icon {
  width: 48px;
  height: 48px;
  border-radius: 10px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #fff;
  font-size: 22px;
  flex-shrink: 0;
}

.stat-value {
  font-size: 22px;
  font-weight: 600;
  color: #1d2129;
}

.stat-title {
  font-size: 13px;
  color: #86909c;
}

.quick-card {
  border-radius: 12px;
}
</style>
