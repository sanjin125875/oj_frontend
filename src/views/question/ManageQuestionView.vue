<template>
  <div id="manageQuestionView">
    <a-card :bordered="false" class="table-card">
      <template #title>
        <span>题目管理</span>
      </template>
      <template #extra>
        <a-button type="primary" @click="router.push('/add/question')">
          <template #icon><icon-plus /></template>
          创建题目
        </a-button>
      </template>
      <a-table
        :columns="columns"
        :data="dataList"
        :scroll="{ x: 1200 }"
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
              v-for="tag of parseJson(record.tags)"
              :key="tag"
              color="arcoblue"
            >
              {{ tag }}
            </a-tag>
          </a-space>
        </template>
        <template #judgeConfig="{ record }">
          <span class="json-cell">{{ formatJson(record.judgeConfig) }}</span>
        </template>
        <template #judgeCase="{ record }">
          <span class="json-cell">{{ formatJson(record.judgeCase) }}</span>
        </template>
        <template #createTime="{ record }">
          {{ formatTime(record.createTime) }}
        </template>
        <template #optional="{ record }">
          <a-space>
            <a-button size="small" type="primary" @click="doUpdate(record)">
              修改
            </a-button>
            <a-popconfirm content="确定要删除该题目吗？" @ok="doDelete(record)">
              <a-button size="small" status="danger">删除</a-button>
            </a-popconfirm>
          </a-space>
        </template>
      </a-table>
    </a-card>
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref } from "vue";
import { IconPlus } from "@arco-design/web-vue/es/icon";
import { Question, QuestionControllerService } from "../../../generated";
import message from "@arco-design/web-vue/es/message";
import { useRouter } from "vue-router";

const dataList = ref([]);
const total = ref(0);
const searchParams = ref({
  pageSize: 10,
  pageNum: 1,
});

const loadData = async () => {
  const res = await QuestionControllerService.listQuestionByPageUsingPost(
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
 * 页面加载时，请求数据
 */
onMounted(() => {
  loadData();
});

/**
 * 分页切换
 */
const onPageChange = (page: number) => {
  searchParams.value = {
    ...searchParams.value,
    pageNum: page,
  };
  loadData();
};

const columns = [
  {
    title: "id",
    dataIndex: "id",
    width: 80,
  },
  {
    title: "标题",
    dataIndex: "title",
    width: 160,
    ellipsis: true,
    tooltip: true,
  },
  {
    title: "内容",
    dataIndex: "content",
    ellipsis: true,
    tooltip: true,
  },
  {
    title: "标签",
    slotName: "tags",
    width: 160,
  },
  {
    title: "答案",
    dataIndex: "answer",
    ellipsis: true,
    tooltip: true,
  },
  {
    title: "提交数",
    dataIndex: "submitNum",
    width: 90,
  },
  {
    title: "通过数",
    dataIndex: "acceptedNum",
    width: 90,
  },
  {
    title: "判题配置",
    slotName: "judgeConfig",
    width: 180,
  },
  {
    title: "判题用例",
    slotName: "judgeCase",
    width: 180,
  },
  {
    title: "创建时间",
    slotName: "createTime",
    width: 170,
  },
  {
    title: "操作",
    slotName: "optional",
    fixed: "right",
    width: 150,
  },
];

/**
 * 解析 JSON 字符串为数组
 */
const parseJson = (str: any): string[] => {
  try {
    return JSON.parse(str) ?? [];
  } catch {
    return [];
  }
};

/**
 * 格式化 JSON 字符串便于展示
 */
const formatJson = (str: any) => {
  try {
    return JSON.stringify(JSON.parse(str));
  } catch {
    return String(str ?? "-");
  }
};

/**
 * 格式化时间
 */
const formatTime = (time: string) => {
  if (!time) return "-";
  return new Date(time).toLocaleString("zh-CN", { hour12: false });
};

const doDelete = async (question: Question) => {
  const res = await QuestionControllerService.deleteQuestionUsingPost({
    id: question.id,
  });
  if (res.code === 0) {
    message.success("删除成功");
    loadData();
  } else {
    message.error("删除失败");
  }
};

const router = useRouter();

const doUpdate = (question: Question) => {
  router.push({
    path: "/update/question",
    query: {
      id: question.id,
    },
  });
};
</script>

<style scoped>
#manageQuestionView {
  max-width: 1400px;
  margin: 0 auto;
}

.table-card {
  border-radius: 12px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.04);
}

.json-cell {
  color: #86909c;
  font-size: 12px;
  font-family: monospace;
}
</style>
