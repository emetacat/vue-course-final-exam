<template>
  <div id="app-layout">
    <!-- 顶部导航 -->
    <!-- 通信: 
         1. Props (父 -> 子): 传递 campuses, activeCampus, title
         2. v-on (子 -> 父): 监听 update-active-campus 事件更新 activeCampus (替代 v-model)
    -->
    <AppHeader
      :campuses="campuses"
      :active-campus="currentCampus"
      :title="appTitle"
      @update-active-campus="handleCampusChange" />

    <main class="main-content">
      <!-- 左侧：科室列表 (侧边栏) -->
      <aside class="sidebar">
        <div class="sidebar-header">
          <i class="fas fa-hospital-user"></i> 选择科室
        </div>
        <!-- 通信:
             1. Props (父 -> 子): 传递 departments, selectedId
             2. v-on (子 -> 父): 监听 select-dept 事件
        -->
        <DeptList
          :departments="departments"
          :selected-id="currentDeptId"
          @select-dept="handleDeptChange" />
      </aside>

      <!-- 右侧：主要操作区 -->
      <section class="content-area">
        <!-- 日期切换 -->
        <!-- 通信:
             1. Props (父 -> 子): 传递 days, selectedIndex
             2. v-on (子 -> 父): 监听 update-day-index 事件更新 currentDayIndex (替代 v-model)
        -->
        <DateTabs
          :days="fixedDays"
          :selected-index="currentDayIndex"
          @update-day-index="handleDayChange" />

        <!-- 医生列表展示区 -->
        <div class="doctors-panel">
          <div class="panel-header">
            <h2 class="panel-title">
              <span class="highlight">{{ currentCampus }}</span> -
              <span>{{ currentDeptName }}</span> -
              <span>{{ currentDayName }}</span> 医生列表
            </h2>
          </div>

          <!-- 列表为空时 -->
          <div v-if="filteredDoctors.length === 0" class="empty-state">
            <i class="fas fa-user-md empty-icon"></i>
            <p>当前筛选条件下暂无出诊医生</p>
          </div>

          <!-- 医生网格 -->
          <div v-else class="doctors-grid">
            <transition-group name="list">
              <!-- 通信:
                   1. Props (父 -> 子): 传递 doctor 对象
                   2. v-on (子 -> 父): 监听 book 事件，接收子组件传回的 doctor 对象
              -->
              <DoctorCard
                v-for="doc in filteredDoctors"
                :key="doc.id"
                :doctor="doc"
                @book="handleBooking">
                <!-- 通信(Slot): 插槽 -> 父组件向子组件分发 HTML 内容 (职称样式) -->
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

// --- 1. 静态资源引入 ---
// 101 心血管内科
import img101_1 from './assets/心血管内科-吴翔.jpg'
import img101_2 from './assets/心血管内科-盛红专.jpg'
import img101_3 from './assets/心血管内科-张剑.jpg'
import img101_4 from './assets/心血管内科-施林生.jpg'

// 102 呼吸与危重症医学科
import img102_1 from './assets/呼吸与危重症医学科-倪松石.jpg'
import img102_2 from './assets/呼吸与危重症医学科-冯健.jpg'
import img102_3 from './assets/呼吸与危重症医学科-许立芹.jpg'
import img102_4 from './assets/呼吸与危重症医学科-周晓宇.jpg'

// 103 消化内科
import img103_1 from './assets/消化内科-倪润洲.jpg'
import img103_2 from './assets/消化内科-陆翠华.jpg'
import img103_3 from './assets/消化内科-石建群.jpg'
import img103_4 from './assets/消化内科-俞智华.jpg'

// 104 手外科
import img104_1 from './assets/手外科-谭军.jpg'
import img104_2 from './assets/手外科-邓爱东.jpg'
import img104_3 from './assets/手外科-龚炎培.jpg'
import img104_4 from './assets/手外科-茅天.jpg'

// 105 神经外科
import img105_1 from './assets/神经外科-施炜.jpg'
import img105_2 from './assets/神经外科-陈建.jpg'
import img105_3 from './assets/神经外科-杨柳.jpg'
import img105_4 from './assets/神经外科-苏星.jpg'

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

      // 医生数据源 (将在 created 中自动填充)
      allDoctors: [],
    }
  },

  created() {
    // 初始化时生成全覆盖的模拟数据
    this.generateFullSchedule()
  },

  computed: {
    // 计算属性：当前选中的科室名称
    currentDeptName() {
      const dept = this.departments.find((d) => d.id === this.currentDeptId)
      return dept ? dept.name : ''
    },
    // 计算属性：当前选中的日期显示文本
    currentDayName() {
      return this.fixedDays[this.currentDayIndex]?.week || ''
    },
    // 计算属性：核心筛选逻辑 (院区 + 科室 + 日期)
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
    /**
     * 逻辑功能：生成覆盖所有院区、科室、日期的排班数据
     * 实现细节：
     * 1. 定义各科室的医生档案池(Profiles)。
     * 2. 遍历 2个院区 * 5个科室 * 7天。
     * 3. 每个时间段随机(或轮询)分配 2-3 名医生。
     */
    generateFullSchedule() {
      // 医生档案池配置
      const doctorProfiles = {
        101: [
          {
            name: '吴翔',
            title: '主任医师',
            fee: 150,
            avatar: img101_1,
            bio: '擅长心血管内科疑难疾病的临床诊断及治疗。尤其是冠心病、高血压、心律失常等疾病的诊断和治疗。',
          },
          {
            name: '盛红专',
            title: '主任医师',
            fee: 100,
            avatar: img101_2,
            bio: '擅长心血管疑难和危重病人诊治，尤其冠心病、肥厚型心肌病和二尖瓣、主动脉瓣病变的介入治疗。',
          },
          {
            name: '张剑',
            title: '副主任医师',
            fee: 50,
            avatar: img101_3,
            bio: '擅长领域：心律失常如室上性心动过速、心房颤动、心房扑动的射频消融治疗、起搏器安装。',
          },
          {
            name: '施林生',
            title: '主治医师',
            fee: 25,
            avatar: img101_4,
            bio: '擅长心血管疾病的诊治，尤其擅长房颤、房速、室早、室上速及室速等复杂心律失常的导管消融。',
          },
        ],
        102: [
          {
            name: '倪松石',
            title: '主任医师',
            fee: 150,
            avatar: img102_1,
            bio: '擅长呼吸与危重症医学科疑难和危重病人诊治以及支气管镜和辅助呼吸诊疗技术。',
          },
          {
            name: '冯健',
            title: '副主任医师',
            fee: 60,
            avatar: img102_2,
            bio: '擅长呼吸系统常见病及疑难危重症疾病的诊疗，尤其是肺结节及肺部肿瘤的诊治。',
          },
          {
            name: '许立芹',
            title: '主治医师',
            fee: 30,
            avatar: img102_3,
            bio: '擅长呼吸系统疾病的诊治、呼吸介入诊疗技术及辅助呼吸技术。',
          },
          {
            name: '周晓宇',
            title: '住院医师',
            fee: 15,
            avatar: img102_4,
            bio: '擅长支气管肺癌、肺部感染性疾病、慢性气道疾病、睡眠呼吸暂停低通气综合征等疾病的诊治。',
          },
        ],
        103: [
          {
            name: '倪润洲',
            title: '主任医师',
            fee: 140,
            avatar: img103_1,
            bio: '擅长消化内科疑难疾病的诊治，尤其是慢性胃炎、胃食管反流病、溃疡性结肠炎等病的中西医结合治疗。',
          },
          {
            name: '陆翠华',
            title: '副主任医师',
            fee: 55,
            avatar: img103_2,
            bio: '擅长小肠疾病的小肠镜下诊断与治疗及消化道癌前病变、消化道出血的内镜下治疗。',
          },
          {
            name: '石建群',
            title: '主治医师',
            fee: 25,
            avatar: img103_3,
            bio: '擅长胃肠肝胆胰等消化系统疾病的诊断、治疗及抢救。',
          },
          {
            name: '俞智华',
            title: '住院医师',
            fee: 12,
            avatar: img103_4,
            bio: '擅长消化系统常见病、多发病的诊断及治疗。',
          },
        ],
        104: [
          {
            name: '谭军',
            title: '主任医师',
            fee: 160,
            avatar: img104_1,
            bio: '擅长肩、肘、腕疾患以及小儿先天肢体畸形的诊断和治疗，尤其擅长关节镜微创手术及创面显微修复。',
          },
          {
            name: '邓爱东',
            title: '副主任医师',
            fee: 70,
            avatar: img104_2,
            bio: '擅长于关节镜下微创修复各类关节运动损伤、上肢骨关节的损伤诊治及矫形重建。',
          },
          {
            name: '龚炎培',
            title: '主治医师',
            fee: 35,
            avatar: img104_3,
            bio: '擅长外周神经损伤修复及后期功能重建。',
          },
          {
            name: '茅天',
            title: '住院医师',
            fee: 18,
            avatar: img104_4,
            bio: '擅长腕关节、肩关节、肘关节、踝关节疾病的关节镜下微创治疗。',
          },
        ],
        105: [
          {
            name: '施炜',
            title: '主任医师',
            fee: 180,
            avatar: img105_1,
            bio: '擅长内镜下垂体瘤等颅底肿瘤的微创手术及内镜下三叉神经痛/面肌痉挛微创手术。',
          },
          {
            name: '陈建',
            title: '副主任医师',
            fee: 80,
            avatar: img105_2,
            bio: '从事神经外科专业临床工作三十余年，在颅脑外伤、脑肿瘤及脑血管病的诊断治疗上积累了丰富的临床经验。',
          },
          {
            name: '杨柳',
            title: '主治医师',
            fee: 40,
            avatar: img105_3,
            bio: '擅长神经外科各类疾病的微创手术治疗。',
          },
          {
            name: '苏星',
            title: '住院医师',
            fee: 20,
            avatar: img105_4,
            bio: '擅长脑肿瘤、椎管内肿瘤、颅脑创伤、脑出血和神经急危重症患者诊治。',
          },
        ],
      }

      let idCounter = 1
      const generatedData = []

      // 遍历所有院区
      this.campuses.forEach((campus) => {
        // 遍历所有科室
        this.departments.forEach((dept) => {
          const profiles = doctorProfiles[dept.id] || []

          // 遍历未来7天
          this.fixedDays.forEach((_, dayIdx) => {
            // 策略：每天安排 2-3 人，轮询使用医生档案
            // 利用 dayIdx 和 dept.id 制造偏移，让不同日期的医生组合看起来不一样
            const count = dayIdx % 2 === 0 ? 3 : 2
            const startIdx = (dept.id + dayIdx) % profiles.length

            for (let i = 0; i < count; i++) {
              const profileIndex = (startIdx + i) % profiles.length
              const profile = profiles[profileIndex]

              // 模拟票数：为了演示，随机给 0-20 张票
              const totalTickets = [0, 5, 10, 20, 30][(idCounter + i) % 5]
              const tickets = totalTickets // 初始剩余等于总数

              generatedData.push({
                id: idCounter++,
                campus: campus,
                deptId: dept.id,
                dayIdx: dayIdx,
                name: profile.name,
                title: profile.title,
                fee: profile.fee,
                avatar: profile.avatar,
                bio: profile.bio,
                totalTickets: totalTickets,
                tickets: tickets,
                available: tickets > 0,
              })
            }
          })
        })
      })

      this.allDoctors = generatedData
    },

    /**
     * 逻辑功能：更新当前选中的院区
     * 通信监听：监听子组件 AppHeader 派发的 update-active-campus 事件
     * 来源组件：AppHeader.vue
     */
    handleCampusChange(camp) {
      this.currentCampus = camp
    },

    /**
     * 逻辑功能：更新当前选中的科室ID
     * 通信监听：监听子组件 DeptList 派发的 select-dept 事件
     * 来源组件：DeptList.vue
     */
    handleDeptChange(id) {
      this.currentDeptId = id
    },

    /**
     * 逻辑功能：更新当前选中的日期索引
     * 通信监听：监听子组件 DateTabs 派发的 update-day-index 事件
     * 来源组件：DateTabs.vue
     */
    handleDayChange(index) {
      this.currentDayIndex = index
    },

    /**
     * 逻辑功能：处理预约挂号逻辑，包括确认弹窗和数据更新（扣减余号、计算排队号）
     * 通信监听：监听子组件 DoctorCard 派发的 book 事件
     * 来源组件：DoctorCard.vue
     */
    handleBooking(doctor) {
      // 原生 Confirm
      const isConfirmed = window.confirm(
        `【预约确认】\n\n医生：${doctor.name}\n职称：${doctor.title}\n挂号费：¥${doctor.fee}\n\n点击“确定”完成挂号。`
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

          // 计算正序序号 (总号源 - 当前剩余号源)
          const rank = target.totalTickets - target.tickets

          window.alert(
            `预约成功！\n您是该医生的第 ${rank} 位患者。\n请按时就诊。`
          )
        }
      }
    },

    /**
     * 逻辑功能：根据医生职称返回对应的样式类名
     * 辅助方法：用于 DoctorCard 插槽内的样式动态绑定
     */
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
