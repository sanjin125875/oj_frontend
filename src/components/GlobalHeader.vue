<template>
  <a-row id="globalHeader" align="center" :wrap="false">
    <a-col flex="auto">
      <a-menu
        mode="horizontal"
        :selected-keys="selectedKeys"
        @menu-item-click="doMenuClick"
      >
        <a-menu-item
          key="0"
          :style="{ padding: 0, marginRight: '38px' }"
          disabled
        >
          <div class="title-bar">
            <img class="logo" src="../assets/oj-logo.svg" />
            <div class="title">OJ</div>
          </div>
        </a-menu-item>
        <a-menu-item v-for="item in visibleRoutes" :key="item.path">
          {{ item.name }}
        </a-menu-item>
      </a-menu>
    </a-col>
    <a-col flex="140px">
      <!-- 已登录：头像 + 用户名下拉菜单 -->
      <a-dropdown trigger="click" v-if="isLogin">
        <div class="user-info">
          <a-avatar :size="28" class="user-avatar">
            <img
              v-if="loginUser?.userAvatar"
              :src="loginUser.userAvatar"
              alt="avatar"
            />
            <template v-else>{{
              (loginUser?.userName || loginUser?.userAccount || "未").charAt(0)
            }}</template>
          </a-avatar>
          <span class="user-name">
            {{ loginUser?.userName || loginUser?.userAccount || "未登录" }}
          </span>
          <icon-down class="user-arrow" />
        </div>
        <template #content>
          <a-doption @click="router.push('/about')">
            <template #icon><icon-info-circle /></template>
            关于系统
          </a-doption>
          <a-doption class="logout-option" @click="doLogout">
            <template #icon><icon-export /></template>
            退出登录
          </a-doption>
        </template>
      </a-dropdown>
      <!-- 未登录：点击跳转登录页 -->
      <div v-else class="user-info" @click="router.push('/user/login')">
        <a-avatar :size="28" class="user-avatar">
          <icon-user />
        </a-avatar>
        <span class="user-name">未登录</span>
      </div>
    </a-col>
  </a-row>
</template>

<script setup lang="ts">
import { routes } from "../router/routes";
import { useRouter } from "vue-router";
import { computed, ref } from "vue";
import { useStore } from "vuex";
import {
  IconDown,
  IconExport,
  IconInfoCircle,
  IconUser,
} from "@arco-design/web-vue/es/icon";
import message from "@arco-design/web-vue/es/message";
import checkAccess from "@/access/checkAccess";
import ACCESS_ENUM from "@/access/accessEnum";
import { UserControllerService } from "../../generated";

const router = useRouter();
const store = useStore();

// 当前登录用户
const loginUser = computed(() => store.state.user?.loginUser);

// 是否已登录
const isLogin = computed(
  () =>
    !!loginUser.value?.userRole &&
    loginUser.value.userRole !== ACCESS_ENUM.NOT_LOGIN
);

/**
 * 退出登录
 */
const doLogout = async () => {
  try {
    const res = await UserControllerService.userLogoutUsingPost();
    if (res.code === 0) {
      store.commit("user/updateUser", {
        userName: "未登录",
        userRole: ACCESS_ENUM.NOT_LOGIN,
      });
      message.success("退出登录成功");
      router.push("/user/login");
    } else {
      message.error("退出登录失败，" + res.message);
    }
  } catch (e: any) {
    message.error("退出登录失败，请检查后端服务：" + (e?.message ?? e));
  }
};

// 展示在菜单的路由数组
const visibleRoutes = computed(() => {
  return routes.filter((item) => {
    if (item.meta?.hideInMenu) {
      return false;
    }
    // 根据权限过滤菜单
    if (
      !checkAccess(store.state.user.loginUser, item?.meta?.access as string)
    ) {
      return false;
    }
    return true;
  });
});

// 默认主页
const selectedKeys = ref(["/"]);

// 路由跳转后，更新选中的菜单项
router.afterEach((to, from, failure) => {
  selectedKeys.value = [to.path];
});

const doMenuClick = (key: string) => {
  router.push({
    path: key,
  });
};
</script>

<style scoped>
.title-bar {
  display: flex;
  align-items: center;
}

.title {
  color: #444;
  margin-left: 16px;
}

.logo {
  height: 48px;
}

.user-info {
  display: flex;
  align-items: center;
  justify-content: flex-end;
  gap: 8px;
  cursor: pointer;
  padding: 4px 8px;
  border-radius: 6px;
  transition: background-color 0.2s;
}

.user-info:hover {
  background-color: #f2f3f5;
}

.user-name {
  font-size: 14px;
  color: #1d2129;
  max-width: 90px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.user-arrow {
  color: #86909c;
  font-size: 12px;
}

.logout-option {
  color: #f53f3f;
}
</style>
