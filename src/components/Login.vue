<template>
  <div class="login-container">
    <div class="login-box">
      <!-- 顶部 Logo 区域 -->
      <div class="login-header">
        <img src="../assets/logo.jpg" alt="Logo" class="login-logo" />
        <h2 class="login-title">用户登录</h2>
      </div>

      <!-- 表单区域 -->
      <form class="login-form" @submit.prevent="handleLoginSubmit">
        <div class="form-group">
          <label for="username">就诊卡号 / 手机号</label>
          <input
            type="text"
            id="username"
            v-model="username"
            placeholder="请输入卡号或手机号"
            class="form-input"
            required />
        </div>

        <div class="form-group">
          <label for="password">密码</label>
          <input
            type="password"
            id="password"
            v-model="password"
            placeholder="请输入密码"
            class="form-input"
            required />
        </div>

        <div class="form-actions">
          <!-- 通信(Emit): 触发 login 事件，将用户名传递给父组件 -->
          <button type="submit" class="login-btn">登录</button>
        </div>
      </form>

      <p class="login-footer">南通大学附属医院挂号平台</p>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Login',
  data() {
    return {
      username: '',
      password: '',
    }
  },
  methods: {
    /**
     * 逻辑功能：处理登录表单提交
     * 1. 简单的非空校验（HTML required 属性实现）
     * 2. 模拟验证通过
     * 3. 通信(Emit): 触发 'login' 事件，将用户名传递给父组件 (App.vue)
     */
    handleLoginSubmit() {
      // 用户名 & 密码 非空校验
      if (this.username && this.password) {
        // 演示：任何非空输入都允许登录
        // 将用户信息传递给父组件
        this.$emit('login', { username: this.username })
      } else {
        alert('请输入用户名和密码')
      }
    },
  },
}
</script>

<style scoped>
.login-container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  background-color: #f0f2f5;
  background-image: linear-gradient(to bottom, #dbeafe, #f0f2f5);
}

.login-box {
  background: white;
  padding: 40px;
  border-radius: 12px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
  width: 100%;
  max-width: 400px;
}

.login-header {
  text-align: center;
  margin-bottom: 30px;
}

.login-logo {
  width: 64px;
  height: 64px;
  border-radius: 50%;
  margin-bottom: 15px;
  border: 2px solid #eff6ff;
}

.login-title {
  font-size: 24px;
  color: #1e3a8a;
  font-weight: bold;
}

.form-group {
  margin-bottom: 20px;
}

.form-group label {
  display: block;
  margin-bottom: 8px;
  color: #4b5563;
  font-size: 14px;
  font-weight: 500;
}

.form-input {
  width: 100%;
  padding: 12px;
  border: 1px solid #d1d5db;
  border-radius: 6px;
  font-size: 16px;
  transition: border-color 0.2s;
  box-sizing: border-box;
}

.form-input:focus {
  border-color: #2563eb;
  outline: none;
  box-shadow: 0 0 0 3px rgba(37, 99, 235, 0.1);
}

.login-btn {
  width: 100%;
  padding: 12px;
  background-color: #2563eb;
  color: white;
  border: none;
  border-radius: 6px;
  font-size: 16px;
  font-weight: bold;
  cursor: pointer;
  transition: background-color 0.2s;
}

.login-btn:hover {
  background-color: #1d4ed8;
}

.login-footer {
  text-align: center;
  margin-top: 25px;
  color: #9ca3af;
  font-size: 12px;
}
</style>
