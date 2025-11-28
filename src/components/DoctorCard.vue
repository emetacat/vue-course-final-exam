<template>
  <div class="doctor-card">
    <!-- 顶部蓝色装饰条 -->
    <div class="card-header-line"></div>

    <div class="card-body">
      <!-- 左侧：头像 -->
      <div class="avatar-wrapper">
        <img :src="doctor.avatar" :alt="doctor.name" class="avatar-img" />
        <!-- 状态小圆点 -->
        <div class="status-dot" :class="{ available: doctor.available }">
          <i v-if="doctor.available" class="fas fa-check"></i>
          <i v-else class="fas fa-times"></i>
        </div>
      </div>

      <!-- 右侧：信息 -->
      <div class="info-wrapper">
        <div class="info-header">
          <h3 class="doctor-name">
            {{ doctor.name }}
            <!-- 通信(Slot): 插槽 -> 接收父组件分发的 HTML 内容(职称Tag) -->
            <slot name="title-badge"></slot>
          </h3>
          <span class="fee-text">¥{{ doctor.fee }}</span>
        </div>

        <div class="doctor-bio">
          {{ doctor.bio }}
        </div>

        <div class="tags-row">
          <!-- 状态 Tag -->
          <span :class="['tag', doctor.available ? 'tag-success' : 'tag-gray']">
            {{ doctor.available ? '有号' : '满号' }}
          </span>

          <!-- 余号展示 -->
          <span class="tag tag-orange"> 余号: {{ doctor.tickets }} </span>

          <!-- 科室 Tag -->
          <span class="tag tag-blue">
            {{ doctor.deptName }}
          </span>
        </div>

        <!-- 通信(Emit): 点击触发 'book' 事件，将当前医生对象传递给父组件 -->
        <button
          @click="$emit('book', doctor)"
          :disabled="!doctor.available"
          :class="[
            'book-btn',
            doctor.available ? 'btn-primary' : 'btn-disabled',
          ]">
          <i class="fas fa-calendar-check"></i>
          {{ doctor.available ? '预约挂号' : '暂停预约' }}
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'DoctorCard',
  // 通信(Props): 接收父组件传递的单个医生对象
  props: {
    doctor: { type: Object, required: true },
  },
}
</script>

<style scoped>
.doctor-card {
  background: white;
  border: 1px solid var(--border-color);
  border-radius: 8px;
  overflow: hidden;
  position: relative;
  transition: transform 0.3s, box-shadow 0.3s;
}

.doctor-card:hover {
  transform: translateY(-2px);
  box-shadow: 0 10px 15px rgba(0, 0, 0, 0.1);
}

.card-header-line {
  height: 4px;
  background-color: var(--primary-color);
  width: 100%;
}

.card-body {
  padding: 16px;
  display: flex;
  gap: 16px;
}

.avatar-wrapper {
  position: relative;
  flex-shrink: 0;
}

.avatar-img {
  width: 64px;
  height: 64px;
  border-radius: 50%;
  object-fit: cover;
  border: 2px solid #f3f4f6;
}

.status-dot {
  position: absolute;
  bottom: 0;
  right: 0;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background-color: #ccc;
  color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 10px;
  border: 2px solid white;
}
.status-dot.available {
  background-color: var(--success-color);
}

.info-wrapper {
  flex: 1;
  min-width: 0;
}

.info-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 4px;
}

.doctor-name {
  font-size: 18px;
  font-weight: bold;
  display: flex;
  align-items: center;
  white-space: nowrap;
  overflow: hidden;
}

.fee-text {
  color: var(--danger-color);
  font-weight: bold;
  font-size: 16px;
}

.doctor-bio {
  font-size: 13px;
  color: var(--text-secondary);
  margin-bottom: 10px;
  height: 36px;
  overflow: hidden;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 2;
  line-clamp: 2;
}

.tags-row {
  display: flex;
  gap: 8px;
  margin-bottom: 12px;
}

.tag {
  font-size: 12px;
  padding: 2px 8px;
  border-radius: 12px;
}
.tag-success {
  background-color: #d1fae5;
  color: #065f46;
}
.tag-gray {
  background-color: #f3f4f6;
  color: #6b7280;
}
.tag-blue {
  background-color: var(--primary-light);
  color: var(--primary-color);
  border: 1px solid #bfdbfe;
}
/* 新增橙色标签样式，用于余号 */
.tag-orange {
  background-color: #fff7ed;
  color: #c2410c;
  border: 1px solid #fed7aa;
}

.book-btn {
  width: 100%;
  padding: 8px 0;
  border-radius: 6px;
  font-size: 14px;
  font-weight: 500;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 6px;
  transition: background-color 0.2s;
}

.btn-primary {
  background-color: var(--primary-color);
  color: white;
  box-shadow: 0 2px 4px rgba(37, 99, 235, 0.3);
}
.btn-primary:hover {
  background-color: #1d4ed8;
}

.btn-disabled {
  background-color: #f3f4f6;
  color: #9ca3af;
  cursor: not-allowed;
}
</style>
