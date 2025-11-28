<template>
  <div class="date-tabs-container">
    <div class="tabs-wrapper">
      <!-- 通信(Emit): 点击触发 'update-day-index' 事件 -> 更新父组件对应的数据 -->
      <div
        v-for="(day, index) in days"
        :key="index"
        @click="handleClick(index)"
        :class="['date-card', { active: selectedIndex === index }]">
        <span class="date-text">{{ day.date }}</span>
        <span class="week-text">{{ day.week }}</span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'DateTabs',
  // 通信(Props): 接收父组件传递的日期数据和当前选中的索引
  props: {
    days: { type: Array, required: true },
    selectedIndex: { type: Number, required: true },
  },
  methods: {
    /**
     * 逻辑功能：响应日期卡片点击
     * 通信(Emit): 触发 'update-day-index' 事件，将选中日期索引传递给父组件 (App.vue)
     */
    handleClick(index) {
      this.$emit('update-day-index', index)
    },
  },
}
</script>

<style scoped>
/* 样式保持不变 */
.date-tabs-container {
  background-color: white;
  padding: 10px;
  border-radius: 8px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.05);
  margin-bottom: 15px;
}

.tabs-wrapper {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  gap: 8px;
}

.date-card {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 10px 5px;
  border: 1px solid #eee;
  border-radius: 6px;
  cursor: pointer;
  transition: all 0.2s;
  min-width: 60px;
}

.date-card:hover {
  border-color: #bfdbfe;
  background-color: #f0f9ff;
}

.date-card.active {
  background-color: var(--primary-color);
  color: white;
  border-color: var(--primary-color);
  transform: translateY(-2px);
  box-shadow: 0 4px 6px rgba(37, 99, 235, 0.2);
}

.date-text {
  font-size: 12px;
  opacity: 0.8;
  margin-bottom: 4px;
}

.week-text {
  font-size: 14px;
  font-weight: bold;
}

/* 移动端适配：允许横向滚动 */
@media (max-width: 768px) {
  .tabs-wrapper {
    display: flex;
    overflow-x: auto;
    padding-bottom: 5px;
  }
  .date-card {
    flex-shrink: 0;
    width: 70px;
  }
}
</style>
