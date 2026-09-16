<template>
  <div id="exampleView">
    <div class="page-header">
      <h2>在线体验</h2>
      <p>在线编写代码、编辑 Markdown，即刻上手</p>
    </div>
    <a-card class="editor-card" :bordered="false" title="代码编辑器">
      <template #title>
        <a-space>
          <icon-code />
          <span>代码编辑器</span>
        </a-space>
      </template>
      <CodeEditor :value="codeValue" :handle-change="onCodeChange" />
    </a-card>
    <a-card class="editor-card" :bordered="false">
      <template #title>
        <a-space>
          <icon-highlight />
          <span>Markdown 编辑器</span>
        </a-space>
      </template>
      <MdEditor :value="mdValue" :handle-change="onMdChange" />
    </a-card>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";
import { IconCode, IconHighlight } from "@arco-design/web-vue/es/icon";
import MdEditor from "@/components/MdEditor.vue";
import CodeEditor from "@/components/CodeEditor.vue"; // @ is an alias to /src

const mdValue = ref();
const codeValue = ref();

const onMdChange = (v: string) => {
  mdValue.value = v;
};

const onCodeChange = (v: string) => {
  codeValue.value = v;
};
</script>

<style scoped>
#exampleView {
  max-width: 1200px;
  margin: 0 auto;
}

.page-header {
  margin-bottom: 16px;
}

.page-header h2 {
  margin: 0 0 4px;
  font-size: 22px;
  font-weight: 600;
  color: #1d2129;
}

.page-header p {
  margin: 0;
  font-size: 13px;
  color: #86909c;
}

.editor-card {
  border-radius: 12px;
  margin-bottom: 16px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.04);
}
</style>
