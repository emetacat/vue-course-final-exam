<template>
  <ul class="dept-list">
    <li v-for="dept in departments" :key="dept.id" class="dept-item">
      <button
        @click="$emit('select-dept', dept.id)"
        :class="['dept-btn', { active: selectedId === dept.id }]">
        {{ dept.name }}
      </button>
    </li>
  </ul>
</template>

<script>
export default {
  name: 'DeptList',
  props: {
    departments: { type: Array, required: true },
    selectedId: { type: Number, required: true },
  },
  emits: ['select-dept'],
}
</script>

<style scoped>
.dept-list {
  display: flex;
  flex-direction: column;
  height: 100%;
  overflow-y: auto;
}

.dept-btn {
  width: 100%;
  text-align: left;
  padding: 14px 16px;
  font-size: 15px;
  color: var(--text-secondary);
  border-left: 4px solid transparent;
  transition: all 0.2s;
  background-color: #fff;
  border-bottom: 1px solid #f0f0f0;
}

.dept-btn:hover {
  background-color: #f9fafb;
}

.dept-btn.active {
  background-color: var(--primary-light);
  color: var(--primary-color);
  border-left-color: var(--primary-color);
  font-weight: bold;
}

/* 移动端适配：变为顶部横向滚动 */
@media (max-width: 768px) {
  .dept-list {
    flex-direction: row;
    overflow-x: auto;
    overflow-y: hidden;
    white-space: nowrap;
    padding-bottom: 5px;
  }

  .dept-item {
    flex-shrink: 0;
  }

  .dept-btn {
    width: auto;
    padding: 10px 16px;
    border-left: none;
    border-bottom: 3px solid transparent;
    border-radius: 0;
  }

  .dept-btn.active {
    border-left: none;
    border-bottom-color: var(--primary-color);
    background-color: transparent;
  }
}
</style>
