<template>
  <div id="questionsView">
    <a-card :bordered="false" class="table-card">
      <template #title>
        <span>题目列表</span>
      </template>
      <!-- 搜索表单：输入与 searchParams 同步，提交按钮绑定修改 searchParams -->
      <a-form layout="inline" :model="searchParams" @submit="doSearch">
        <a-form-item field="title" label="题目标题">
          <a-input
            v-model="searchParams.title"
            placeholder="请输入题目标题"
            allow-clear
          />
        </a-form-item>
        <a-form-item field="tags" label="标签">
          <a-input-tag
            v-model="searchParams.tags"
            placeholder="输入标签，回车添加"
            allow-clear
          />
        </a-form-item>
        <a-form-item>
          <a-button type="primary" html-type="submit">搜索</a-button>
        </a-form-item>
      </a-form>
      <a-divider :margin="12" />
      <a-table
        :columns="columns"
        :data="dataList"
        :pagination="{
          showTotal: true,
          pageSize: searchParams.pageSize,
          current: searchParams.pageNum,
          total,
        }"
        @page-change="onPageChange"
      >
        <template #tags="{ record }">
          <a-space wrap size="small">
            <a-tag
              v-for="tag of parseTags(record.tags)"
              :key="tag"
              color="arcoblue"
            >
              {{ tag }}
            </a-tag>
          </a-space>
        </template>
        <template #acceptRate="{ record }">
          {{ calcAcceptRate(record) }}
        </template>
        <template #createTime="{ record }">
          {{ moment(record.createTime).format("YYYY-MM-DD HH:mm:ss") }}
        </template>
        <template #optional="{ record }">
          <a-space>
            <a-button
              size="small"
              type="primary"
              @click="router.push(`/view/question/${record.id}`)"
            >
              做题
            </a-button>
          </a-space>
        </template>
      </a-table>
    </a-card>
  </div>
</template>

<script setup lang="ts">
import { ref, watchEffect } from "vue";
import { QuestionControllerService } from "../../../generated";
import message from "@arco-design/web-vue/es/message";
import { useRouter } from "vue-router";
import moment from "moment";

const router = useRouter();

const dataList = ref([]);
const total = ref(0);
const searchParams = ref({
  title: "",
  tags: [],
  pageNum: 1,
  pageSize: 10,
});

const loadData = async () => {
  const res = await QuestionControllerService.listQuestionVoByPageUsingPost(
    searchParams.value
  );
  if (res.code === 0) {
    dataList.value = res.data.records;
    total.value = res.data.total;
  } else {
    message.error("加载失败，" + res.message);
  }
};

/**
 * watchEffect 监听 searchParams 的变化，自动触发查询
 * （首次立即执行；提交搜索 / 页号变化时修改 searchParams 即可）
 */
watchEffect(() => {
  loadData();
});

/**
 * 提交搜索：修改 searchParams，由 watchEffect 监听触发查询
 */
const doSearch = () => {
  searchParams.value = {
    ...searchParams.value,
    pageNum: 1,
  };
};

/**
 * 分页切换：修改 searchParams，由 watchEffect 监听触发查询
 */
const onPageChange = (page: number) => {
  searchParams.value = {
    ...searchParams.value,
    pageNum: page,
  };
};

const columns = [
  {
    title: "题号",
    dataIndex: "id",
    width: 100,
  },
  {
    title: "标题",
    dataIndex: "title",
    ellipsis: true,
    tooltip: true,
  },
  {
    title: "标签",
    slotName: "tags",
    width: 220,
  },
  {
    title: "通过率",
    slotName: "acceptRate",
    width: 120,
  },
  {
    title: "创建时间",
    slotName: "createTime",
    width: 180,
  },
  {
    title: "操作",
    slotName: "optional",
    width: 100,
  },
];

/**
 * 解析标签：兼容数组或 JSON 字符串两种返回格式
 */
const parseTags = (tags: any): string[] => {
  if (Array.isArray(tags)) {
    return tags;
  }
  try {
    return JSON.parse(tags) ?? [];
  } catch {
    return [];
  }
};

/**
 * 计算通过率：通过数 / 提交数
 */
const calcAcceptRate = (record: any) => {
  if (!record.submitNum || record.submitNum === 0) {
    return "-";
  }
  return ((record.acceptedNum / record.submitNum) * 100).toFixed(1) + "%";
};
</script>

<style scoped>
#questionsView {
  max-width: 1200px;
  margin: 0 auto;
}

.table-card {
  border-radius: 12px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.04);
}
</style>
