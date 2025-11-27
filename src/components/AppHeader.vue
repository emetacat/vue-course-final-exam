<template>
  <header class="app-header">
    <div class="header-container">
      <!-- 左侧 Logo 和标题 -->
      <div class="logo-area">
        <!-- 使用 img 标签引入通大附院 logo 图片 -->
        <img src="../assets/logo.jpg" alt="Logo" class="logo-img" />
        <h1 class="app-title">{{ title }}</h1>
      </div>

      <!-- 右侧 院区切换 -->
      <div class="campus-switch">
        <button
          v-for="camp in campuses"
          :key="camp"
          @click="$emit('update:activeCampus', camp)"
          :class="['camp-btn', { active: activeCampus === camp }]">
          {{ camp }}
        </button>
      </div>
    </div>
  </header>
</template>

<script>
export default {
  name: 'AppHeader',
  props: {
    campuses: { type: Array, required: true },
    activeCampus: { type: String, required: true },
    title: { type: String, default: '医院挂号平台' },
  },
  emits: ['update:activeCampus'],
}
</script>

<style scoped>
.app-header {
  background: linear-gradient(to right, #1e40af, #3b82f6); /* 蓝色渐变 */
  color: white;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.15);
  position: sticky;
  top: 0;
  z-index: 100;
}

.header-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 12px 16px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.logo-area {
  display: flex;
  align-items: center;
  gap: 12px;
}

/* 修改：Logo 图片样式适配 */
.logo-img {
  width: 40px; /* 稍微加大尺寸 */
  height: 40px;
  border-radius: 50%; /* 圆形，如不需要可改为 4px 或去掉 */
  object-fit: cover; /* 保持比例填充 */
  background-color: white; /* 图片加载前的背景色 */
  border: 2px solid rgba(255, 255, 255, 0.2); /* 增加一个淡淡的边框使其在蓝色背景上更协调 */
}

.app-title {
  font-size: 18px;
  font-weight: 600;
  letter-spacing: 1px;
}

.campus-switch {
  background-color: rgba(0, 0, 0, 0.2);
  padding: 4px;
  border-radius: 6px;
  display: flex;
}

.camp-btn {
  padding: 6px 16px;
  border-radius: 4px;
  color: rgba(255, 255, 255, 0.8);
  font-size: 14px;
  transition: all 0.3s;
}

.camp-btn:hover {
  background-color: rgba(255, 255, 255, 0.1);
}

.camp-btn.active {
  background-color: white;
  color: var(--primary-color);
  font-weight: bold;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

/* 移动端适配 */
@media (max-width: 600px) {
  .header-container {
    flex-direction: column;
    gap: 10px;
  }
}
</style>
