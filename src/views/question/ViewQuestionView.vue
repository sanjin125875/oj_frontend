<template>
  <div id="viewQuestionView">
    <a-row :gutter="16">
      <!-- 左侧：题目信息 -->
      <a-col :md="12" :xs="24">
        <a-card :bordered="false" class="question-card">
          <template #title>
            <span>{{ question.title || "题目加载中..." }}</span>
          </template>
          <a-space wrap size="small" class="tag-space">
            <a-tag
              v-for="tag of question.tags || []"
              :key="tag"
              color="arcoblue"
            >
              {{ tag }}
            </a-tag>
          </a-space>
          <!-- 判题配置 -->
          <a-descriptions
            title="判题配置"
            :column="{ xs: 1, md: 3 }"
            bordered
            size="small"
            class="judge-config"
          >
            <a-descriptions-item label="时间限制">
              {{ question.judgeConfig?.timeLimit ?? "-" }} ms
            </a-descriptions-item>
            <a-descriptions-item label="内存限制">
              {{ question.judgeConfig?.memoryLimit ?? "-" }} MB
            </a-descriptions-item>
            <a-descriptions-item label="堆栈限制">
              {{ question.judgeConfig?.stackLimit ?? "-" }} KB
            </a-descriptions-item>
          </a-descriptions>
          <!-- tabs 切换展示的内容 -->
          <a-tabs default-active-key="content">
            <a-tab-pane key="content" title="题目内容">
              <MdViewer :value="question.content || ''" />
            </a-tab-pane>
            <a-tab-pane key="answer" title="答案">
              <MdViewer :value="question.answer || '暂无答案'" />
            </a-tab-pane>
          </a-tabs>
        </a-card>
      </a-col>
      <!-- 右侧：代码编辑器 -->
      <a-col :md="12" :xs="24">
        <a-card :bordered="false" class="editor-card">
          <template #title>
            <span>在线答题</span>
          </template>
          <a-form layout="inline" class="language-form">
            <a-form-item label="编程语言">
              <a-select v-model="language" :style="{ width: '160px' }">
                <a-option value="java">Java</a-option>
                <a-option value="cpp">C++</a-option>
                <a-option value="go">Go</a-option>
                <a-option value="python">Python</a-option>
              </a-select>
            </a-form-item>
          </a-form>
          <CodeEditor
            :value="code"
            :language="language"
            :handle-change="onCodeChange"
          />
          <a-divider />
          <a-button
            type="primary"
            size="large"
            style="min-width: 160px"
            @click="doSubmit"
          >
            提交题目
          </a-button>
        </a-card>
      </a-col>
    </a-row>
  </div>
</template>

<script setup lang="ts">
import { defineProps, onMounted, ref } from "vue";
import MdViewer from "@/components/MdViewer.vue";
import CodeEditor from "@/components/CodeEditor.vue";
import {
  QuestionControllerService,
  QuestionSubmitControllerService,
} from "../../../generated";
import message from "@arco-design/web-vue/es/message";

/**
 * 路由开启了 props: true，可直接从 props 中获取动态参数（题目 id）
 */
const props = defineProps<{ id: string }>();

const question = ref<any>({});
const code = ref("");
const language = ref("java");

/**
 * 根据题目 id 获取题目信息
 */
const loadData = async () => {
  if (!props.id) {
    return;
  }
  const res = await QuestionControllerService.getQuestionVoByIdUsingGet(
    props.id as string
  );
  if (res.code === 0) {
    question.value = res.data ?? {};
  } else {
    message.error("加载题目失败，" + res.message);
  }
};

onMounted(() => {
  loadData();
});

const onCodeChange = (value: string) => {
  code.value = value;
};

/**
 * 提交代码
 */
const doSubmit = async () => {
  if (!code.value) {
    message.error("请先编写代码");
    return;
  }
  const res = await QuestionSubmitControllerService.doQuestionSubmitUsingPost({
    questionId: Number(props.id),
    language: language.value,
    code: code.value,
  } as any);
  if (res.code === 0) {
    message.success("提交成功，请稍后查看判题结果");
  } else {
    message.error("提交失败，" + res.message);
  }
};
</script>

<style scoped>
#viewQuestionView {
  max-width: 1400px;
  margin: 0 auto;
}

.question-card,
.editor-card {
  border-radius: 12px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.04);
  margin-bottom: 16px;
}

.tag-space {
  margin-bottom: 12px;
}

.judge-config {
  margin-bottom: 16px;
}

.language-form {
  margin-bottom: 12px;
}
</style>
