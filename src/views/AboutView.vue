<template>
  <div id="aboutView">
    <a-card class="about-card" :bordered="false">
      <div class="about-header">
        <img class="logo" src="../assets/oj-logo.svg" alt="logo" />
        <h1 class="title">OJ</h1>
        <p class="subtitle">在线判题系统 · Online Judge</p>
      </div>
      <a-divider />
      <p class="desc">
        一个基于 Vue 3 + Arco Design 构建的在线判题系统，支持题目管理、
        代码在线编写与自动评测，帮助你高效提升编程能力。
      </p>
      <h3 class="section-title">技术栈</h3>
      <a-space wrap size="medium">
        <a-tag color="arcoblue">Vue 3</a-tag>
        <a-tag color="arcoblue">TypeScript</a-tag>
        <a-tag color="arcoblue">Arco Design</a-tag>
        <a-tag color="arcoblue">Vuex</a-tag>
        <a-tag color="green">Spring Boot</a-tag>
        <a-tag color="green">MySQL</a-tag>
        <a-tag color="green">Redis</a-tag>
        <a-tag color="green">Docker</a-tag>
      </a-space>
      <a-divider />
      <div class="about-footer">
        <a href="https://yupi.icu" target="_blank">
          了解更多 · 编程导航知识星球
        </a>
      </div>
    </a-card>
  </div>
</template>

<style scoped>
#aboutView {
  display: flex;
  justify-content: center;
  padding: 24px 0;
}

.about-card {
  width: 640px;
  border-radius: 12px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.06);
}

.about-header {
  text-align: center;
}

.about-header .logo {
  width: 72px;
  height: 72px;
}

.about-header .title {
  margin: 8px 0 4px;
  font-size: 24px;
  font-weight: 600;
  color: #1d2129;
}

.about-header .subtitle {
  margin: 0;
  color: #86909c;
  font-size: 13px;
}

.desc {
  color: #4e5969;
  line-height: 1.8;
}

.section-title {
  color: #1d2129;
  font-weight: 600;
}

.about-footer {
  text-align: center;
}

.about-footer a {
  color: #165dff;
  text-decoration: none;
}
</style>
