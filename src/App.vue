<template>
  <div id="app-layout">
    <!-- 顶部导航 -->
    <AppHeader
      :campuses="campuses"
      v-model:active-campus="currentCampus"
      :title="appTitle" />

    <main class="main-content">
      <!-- 左侧：科室列表 (侧边栏) -->
      <aside class="sidebar">
        <div class="sidebar-header">
          <i class="fas fa-hospital-user"></i> 选择科室
        </div>
        <DeptList
          :departments="departments"
          :selected-id="currentDeptId"
          @select-dept="handleDeptChange" />
      </aside>

      <!-- 右侧：主要操作区 -->
      <section class="content-area">
        <!-- 日期切换 -->
        <DateTabs :days="fixedDays" v-model="currentDayIndex" />

        <!-- 医生列表展示区 -->
        <div class="doctors-panel">
          <div class="panel-header">
            <h2 class="panel-title">
              <span class="highlight">{{ currentCampus }}</span> -
              <span>{{ currentDeptName }}</span> -
              <span>{{ currentDayName }}</span> 医生列表
            </h2>
            <span class="count-text"
              >共找到 {{ filteredDoctors.length }} 位医生</span
            >
          </div>

          <!-- 列表为空时 -->
          <div v-if="filteredDoctors.length === 0" class="empty-state">
            <i class="fas fa-user-md empty-icon"></i>
            <p>当前筛选条件下暂无出诊医生</p>
          </div>

          <!-- 医生网格 -->
          <div v-else class="doctors-grid">
            <transition-group name="list">
              <DoctorCard
                v-for="doc in filteredDoctors"
                :key="doc.id"
                :doctor="doc"
                @book="handleBooking">
                <!-- 插槽：不同职称显示不同颜色的边框 -->
                <template #title-badge>
                  <span :class="['title-tag', getTitleClass(doc.title)]">
                    {{ doc.title }}
                  </span>
                </template>
              </DoctorCard>
            </transition-group>
          </div>
        </div>
      </section>
    </main>
  </div>
</template>

<script>
import AppHeader from './components/AppHeader.vue'
import DeptList from './components/DeptList.vue'
import DateTabs from './components/DateTabs.vue'
import DoctorCard from './components/DoctorCard.vue'

// 引入图片
import imgDr1 from './assets/心血管内科-吴翔.jpg'
import imgDr2 from './assets/心血管内科-盛红专.jpg'
import imgDr3 from './assets/心血管内科-张剑.jpg'
import imgDr4 from './assets/心血管内科-施林生.jpg'

export default {
  name: 'App',
  components: {
    AppHeader,
    DeptList,
    DateTabs,
    DoctorCard,
  },
  data() {
    return {
      appTitle: '南通大学附属医院',
      campuses: ['西院区', '东院区'],
      currentCampus: '西院区',

      // 对象数组 - 科室列表
      departments: [
        { id: 101, name: '心血管内科' },
        { id: 102, name: '呼吸与危重症医学科' },
        { id: 103, name: '消化内科' },
        { id: 104, name: '手外科' },
        { id: 105, name: '神经外科' },
      ],
      currentDeptId: 101,

      currentDayIndex: 0,
      fixedDays: [
        { date: '11-22', week: '今天' },
        { date: '11-23', week: '周日' },
        { date: '11-24', week: '周一' },
        { date: '11-25', week: '周二' },
        { date: '11-26', week: '周三' },
        { date: '11-27', week: '周四' },
        { date: '11-28', week: '周五' },
      ],

      // TODO
      // 101-心血管内科
      allDoctors: [
        {
          id: 1,
          name: '吴翔',
          campus: '西院区',
          deptId: 101,
          dayIdx: 0,
          title: '主任医师',
          fee: 150,
          available: true,
          tickets: 10,
          avatar: imgDr1,
          bio: '擅长心血管内科疑难疾病的临床诊断及治疗。尤其是冠心病、高血压、心律失常等疾病的诊断和治疗。',
        },
        {
          id: 2,
          name: '盛红专',
          campus: '西院区',
          deptId: 101,
          dayIdx: 0,
          title: '主任医师',
          fee: 35,
          available: false,
          tickets: 0,
          avatar: imgDr2,
          bio: '擅长心血管疑难和危重病人诊治，尤其冠心病、肥厚型心肌病和二尖瓣、主动脉瓣病变的介入治疗。',
        },
        {
          id: 3,
          name: '张剑',
          campus: '东院区',
          deptId: 101,
          dayIdx: 1,
          title: '副主任医师',
          fee: 50,
          available: true,
          tickets: 5,
          avatar: imgDr3,
          bio: '擅长领域：心律失常如室上性心动过速、心房颤动、心房扑动的射频消融治疗、起搏器安装，以及心血管内科常见病的诊治。',
        },
        {
          id: 4,
          name: '施林生',
          campus: '东院区',
          deptId: 101,
          dayIdx: 0,
          title: '主治医师',
          fee: 12,
          available: true,
          tickets: 50,
          avatar: imgDr4,
          bio: '擅长心血管疾病的诊治，尤其擅长房颤、房速、室早、室上速及室速等复杂心律失常的导管消融，房颤左心耳封堵，缓慢心律失常的起搏治疗。',
        },

        // 102-呼吸与危重症医学科

        // 103-消化内科

        // 104-手外科

        // 105-神经外科
      ],
    }
  },

  computed: {
    currentDeptName() {
      const dept = this.departments.find((d) => d.id === this.currentDeptId)
      return dept ? dept.name : ''
    },
    currentDayName() {
      return this.fixedDays[this.currentDayIndex]?.week || ''
    },
    filteredDoctors() {
      return this.allDoctors
        .filter((doc) => {
          return (
            doc.campus === this.currentCampus &&
            doc.deptId === this.currentDeptId &&
            doc.dayIdx === this.currentDayIndex
          )
        })
        .map((doc) => ({
          ...doc,
          deptName: this.currentDeptName,
        }))
    },
  },

  methods: {
    handleDeptChange(id) {
      this.currentDeptId = id
    },

    handleBooking(doctor) {
      // 检查余号逻辑
      if (doctor.tickets <= 0) {
        window.alert('非常抱歉，该医生已无余号。')
        return
      }

      // 原生 Confirm
      const isConfirmed = window.confirm(
        `【预约确认】\n\n医生：${doctor.name}\n职称：${doctor.title}\n余号：${doctor.tickets}\n挂号费：¥${doctor.fee}\n\n点击“确定”完成挂号。`
      )

      if (isConfirmed) {
        // 更新数据状态：找到原数组中的对象进行修改
        const target = this.allDoctors.find((d) => d.id === doctor.id)

        if (target) {
          target.tickets = target.tickets - 1 // 余号减 1

          // 如果减完之后是0，自动设为不可约
          if (target.tickets <= 0) {
            target.available = false
          }

          window.alert(
            `预约成功！\n您是该医生的倒数第 ${
              target.tickets + 1
            } 位患者。\n请按时就诊。`
          )
        }
      }
    },

    getTitleClass(title) {
      if (title.includes('主任')) return 'tag-blue'
      if (title.includes('主治')) return 'tag-green'
      return 'tag-normal'
    },
  },
}
</script>

<style scoped>
/* 布局 */
#app-layout {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

.main-content {
  flex: 1;
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
  display: flex;
  gap: 20px;
}

/* 左侧侧边栏 */
.sidebar {
  width: 240px;
  background: white;
  border-radius: 8px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  display: flex;
  flex-direction: column;
  flex-shrink: 0;
  overflow: hidden;
  height: fit-content;
  max-height: calc(100vh - 100px);
  position: sticky;
  top: 80px;
}

.sidebar-header {
  padding: 16px;
  background-color: #eff6ff;
  color: #1e40af;
  font-weight: bold;
  border-bottom: 1px solid #e5e7eb;
}

/* 右侧内容区 */
.content-area {
  flex: 1;
  display: flex;
  flex-direction: column;
  min-width: 0; /* 关键：防止 grid 撑开 flex */
}

.doctors-panel {
  background: white;
  border-radius: 8px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  padding: 20px;
  min-height: 400px;
}

.panel-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 1px solid #eee;
  padding-bottom: 15px;
  margin-bottom: 20px;
}

.panel-title {
  font-size: 18px;
  color: #374151;
}

.highlight {
  color: var(--primary-color);
  font-weight: bold;
}

.count-text {
  font-size: 14px;
  color: #6b7280;
}

/* 医生 Grid 布局 */
.doctors-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  gap: 20px;
}

/* 空状态 */
.empty-state {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 200px;
  color: #9ca3af;
}

.empty-icon {
  font-size: 48px;
  margin-bottom: 16px;
  opacity: 0.5;
}

/* 职称 Tag 样式 */
.title-tag {
  font-size: 12px;
  padding: 1px 6px;
  border-radius: 4px;
  margin-left: 8px;
  border: 1px solid;
  font-weight: normal;
}
.tag-blue {
  color: var(--primary-color);
  border-color: #bfdbfe;
  background-color: #eff6ff;
}
.tag-green {
  color: #059669;
  border-color: #a7f3d0;
  background-color: #ecfdf5;
}
.tag-normal {
  color: #4b5563;
  border-color: #e5e7eb;
  background-color: #f9fafb;
}

/* 响应式布局 */
@media (max-width: 768px) {
  .main-content {
    flex-direction: column;
    padding: 10px;
  }

  .sidebar {
    width: 100%;
    position: static;
    max-height: none;
  }

  .doctors-grid {
    grid-template-columns: 1fr; /* 手机端单列 */
  }
}
</style>
