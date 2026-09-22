<template>
  <div id="addQuestionView">
    <a-card :bordered="false" class="form-card">
      <template #title>
        <span>{{ updatePage ? "更新题目" : "创建题目" }}</span>
      </template>
      <a-form :model="form" label-align="left" auto-label-width>
        <a-form-item field="title" label="标题">
          <a-input
            v-model="form.title"
            placeholder="请输入题目标题"
            size="large"
          />
        </a-form-item>
        <a-form-item field="tags" label="标签">
          <a-input-tag
            v-model="form.tags"
            placeholder="输入后回车添加标签，如：简单、二叉树"
            allow-clear
          />
        </a-form-item>
        <a-form-item field="content" label="题目内容">
          <MdEditor :value="form.content" :handle-change="onContentChange" />
        </a-form-item>
        <a-form-item field="answer" label="答案">
          <MdEditor :value="form.answer" :handle-change="onAnswerChange" />
        </a-form-item>

        <a-divider orientation="left">判题配置</a-divider>
        <a-row :gutter="16">
          <a-col :span="8">
            <a-form-item
              field="judgeConfig.timeLimit"
              label="时间限制"
              tooltip="单位：毫秒"
            >
              <a-input-number
                v-model="form.judgeConfig.timeLimit"
                placeholder="请输入时间限制"
                mode="button"
                min="0"
                size="large"
              >
                <template #suffix>ms</template>
              </a-input-number>
            </a-form-item>
          </a-col>
          <a-col :span="8">
            <a-form-item
              field="judgeConfig.memoryLimit"
              label="内存限制"
              tooltip="单位：MB"
            >
              <a-input-number
                v-model="form.judgeConfig.memoryLimit"
                placeholder="请输入内存限制"
                mode="button"
                min="0"
                size="large"
              >
                <template #suffix>MB</template>
              </a-input-number>
            </a-form-item>
          </a-col>
          <a-col :span="8">
            <a-form-item
              field="judgeConfig.stackLimit"
              label="堆栈限制"
              tooltip="单位：KB"
            >
              <a-input-number
                v-model="form.judgeConfig.stackLimit"
                placeholder="请输入堆栈限制"
                mode="button"
                min="0"
                size="large"
              >
                <template #suffix>KB</template>
              </a-input-number>
            </a-form-item>
          </a-col>
        </a-row>

        <a-divider orientation="left">测试用例</a-divider>
        <a-card
          v-for="(judgeCaseItem, index) of form.judgeCase"
          :key="index"
          class="case-card"
          :bordered="true"
          :header-style="{ padding: '8px 16px' }"
        >
          <template #title>
            <span class="case-title">测试用例 {{ index + 1 }}</span>
          </template>
          <template #extra>
            <a-button
              size="small"
              status="danger"
              type="text"
              @click="handleDelete(index)"
            >
              <template #icon><icon-delete /></template>
              删除
            </a-button>
          </template>
          <a-form-item
            :field="`form.judgeCase[${index}].input`"
            label="输入用例"
          >
            <a-input
              v-model="judgeCaseItem.input"
              placeholder="请输入测试输入用例"
              allow-clear
            />
          </a-form-item>
          <a-form-item
            :field="`form.judgeCase[${index}].output`"
            label="输出用例"
          >
            <a-input
              v-model="judgeCaseItem.output"
              placeholder="请输入测试输出用例"
              allow-clear
            />
          </a-form-item>
        </a-card>
        <div class="add-case-btn">
          <a-button type="outline" status="success" @click="handleAdd">
            <template #icon><icon-plus /></template>
            新增测试用例
          </a-button>
        </div>

        <a-divider />
        <a-form-item>
          <a-button
            type="primary"
            size="large"
            style="min-width: 200px"
            @click="doSubmit"
          >
            {{ updatePage ? "保存修改" : "提交创建" }}
          </a-button>
        </a-form-item>
      </a-form>
    </a-card>
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref } from "vue";
import { IconDelete, IconPlus } from "@arco-design/web-vue/es/icon";
import MdEditor from "@/components/MdEditor.vue";
import { QuestionControllerService } from "../../../generated";
import message from "@arco-design/web-vue/es/message";
import { useRoute } from "vue-router";

const route = useRoute();
// 如果页面地址包含 update，视为更新页面
const updatePage = route.path.includes("update");

let form = ref({
  title: "", // 标题
  tags: [], // 标签
  answer: "", // 答案
  content: "", // 内容
  // 判题配置
  judgeConfig: {
    memoryLimit: 1000, // 内存限制
    stackLimit: 1000, // 栈限制
    timeLimit: 1000, // 时间限制
  },
  // 测试用例
  judgeCase: [
    {
      input: "", // 输入
      output: "", // 输出
    },
  ],
});

/**
 * 根据题目 id 获取老的数据
 */
const loadData = async () => {
  const id = route.query.id;
  if (!id) {
    return;
  }
  const res = await QuestionControllerService.getQuestionVoByIdUsingGet(
    id as any
  );
  if (res.code === 0) {
    form.value = res.data as any;

    // 后端已返回数组，直接兜底即可，无需 JSON.parse
    if (!form.value.judgeCase || form.value.judgeCase.length === 0) {
      form.value.judgeCase = [{ input: "", output: "" }];
    }

    if (!form.value.judgeConfig) {
      form.value.judgeConfig = {
        memoryLimit: 1000,
        stackLimit: 1000,
        timeLimit: 1000,
      };
    }

    if (!form.value.tags) {
      form.value.tags = [];
    }
  } else {
    message.error("加载失败，" + res.message);
  }
};

onMounted(() => {
  loadData();
});

const doSubmit = async () => {
  // 区分更新还是创建
  if (updatePage) {
    const res = await QuestionControllerService.updateQuestionUsingPost(
      form.value
    );
    if (res.code === 0) {
      message.success("更新成功");
    } else {
      message.error("更新失败，" + res.message);
    }
  } else {
    const res = await QuestionControllerService.addQuestionUsingPost(
      form.value
    );
    if (res.code === 0) {
      message.success("创建成功");
    } else {
      message.error("创建失败，" + res.message);
    }
  }
};

/**
 * 新增判题用例
 */
const handleAdd = () => {
  form.value.judgeCase.push({
    input: "",
    output: "",
  });
};

/**
 * 删除判题用例
 */
const handleDelete = (index: number) => {
  form.value.judgeCase.splice(index, 1);
};

const onContentChange = (value: string) => {
  form.value.content = value;
};

const onAnswerChange = (value: string) => {
  form.value.answer = value;
};
</script>

<style scoped>
#addQuestionView {
  max-width: 1100px;
  margin: 0 auto;
}

.form-card {
  border-radius: 12px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.04);
}

.case-card {
  border-radius: 8px;
  margin-bottom: 12px;
  background: #fafbfc;
}

.case-title {
  font-size: 14px;
  font-weight: 600;
  color: #1d2129;
}

.add-case-btn {
  margin-top: 4px;
}
</style>
