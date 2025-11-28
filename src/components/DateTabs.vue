<template>
  <div class="date-tabs-container">
    <div class="tabs-wrapper">
      <!-- 通信(Emit): 点击触发 'update:modelValue' 事件 -> 更新父组件 v-model 绑定的数据 -->
      <div
        v-for="(day, index) in days"
        :key="index"
        @click="$emit('update:modelValue', index)"
        :class="['date-card', { active: modelValue === index }]">
        <span class="date-text">{{ day.date }}</span>
        <span class="week-text">{{ day.week }}</span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'DateTabs',
  // 通信(Props): 接收父组件传递的日期数据
  // 通信(v-model): modelValue 是 Vue3 v-model 的默认 prop 名称
  props: {
    days: { type: Array, required: true },
    modelValue: { type: Number, required: true },
  },
}
</script>

<style scoped>
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
