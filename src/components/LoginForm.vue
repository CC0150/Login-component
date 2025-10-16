<template>
  <div class="login-form-wrapper">
    <div class="login-form-container">
      <div class="login-form-header">
        <h2>{{ title }}</h2>
        <p v-if="subtitle">{{ subtitle }}</p>
      </div>
      
      <el-form
        ref="loginFormRef"
        :model="loginForm"
        :rules="rules"
        status-icon
        label-position="left"
        label-width="0"
        class="login-form"
        @submit.prevent
      >
        <!-- 用户名/邮箱输入框 -->
        <el-form-item prop="username">
          <el-input
            v-model="loginForm.username"
            :placeholder="usernamePlaceholder || '请输入用户名/邮箱'"
            :prefix-icon="userIcon"
            :disabled="loading"
            clearable
            @keyup.enter="handleLogin"
            @input="handleInput('username')"
          />
        </el-form-item>

        <!-- 密码输入框 -->
        <el-form-item prop="password">
          <el-input
            v-model="loginForm.password"
            type="password"
            placeholder="请输入密码"
            :prefix-icon="passwordIcon"
            :disabled="loading"
            show-password
            @keyup.enter="handleLogin"
            @input="handleInput('password')"
          />
        </el-form-item>

        <!-- 记住密码和忘记密码 -->
        <div class="login-form-options">
          <el-checkbox v-model="loginForm.remember" :disabled="loading">
            记住密码
          </el-checkbox>
          <el-link type="primary" :disabled="loading" @click="handleForgotPassword">
            忘记密码？
          </el-link>
        </div>

        <!-- 登录按钮 -->
        <el-form-item>
          <el-button
            type="primary"
            :loading="loading"
            :disabled="loading"
            class="login-btn"
            @click="handleLogin"
          >
            {{ loginButtonText }}
          </el-button>
        </el-form-item>

        <!-- 其他登录方式 -->
        <div v-if="showOtherLoginMethods" class="login-form-other">
          <div class="login-form-other-divider">
            <span>其他登录方式</span>
          </div>
          <div class="login-form-other-methods">
            <el-icon v-for="method in otherLoginMethods" :key="method.key" class="login-icon" @click="handleOtherLogin(method)">
              <component :is="method.icon" />
            </el-icon>
          </div>
        </div>

        <!-- 注册链接 -->
        <div v-if="showRegisterLink" class="login-form-register">
          <span>还没有账号？</span>
          <el-link type="primary" @click="handleRegister">立即注册</el-link>
        </div>
      </el-form>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, onMounted, computed } from 'vue'
import { User, Lock, Monitor, Phone, Message } from '@element-plus/icons-vue'

// 定义属性
const props = defineProps({
  // 登录表单标题
  title: {
    type: String,
    default: '账号登录'
  },
  // 登录表单副标题
  subtitle: {
    type: String,
    default: ''
  },
  // 登录按钮文字
  loginButtonText: {
    type: String,
    default: '登录'
  },
  // 用户名占位符
  usernamePlaceholder: {
    type: String,
    default: '请输入用户名/邮箱'
  },
  // 用户名图标类型: user, phone, mail
  usernameType: {
    type: String,
    default: 'user'
  },
  // 是否显示记住密码
  showRememberPassword: {
    type: Boolean,
    default: true
  },
  // 是否显示忘记密码链接
  showForgotPassword: {
    type: Boolean,
    default: true
  },
  // 是否显示注册链接
  showRegisterLink: {
    type: Boolean,
    default: true
  },
  // 是否显示其他登录方式
  showOtherLoginMethods: {
    type: Boolean,
    default: false
  },
  // 其他登录方式配置
  otherLoginMethods: {
    type: Array,
    default: () => [
      { key: 'wechat', icon: Monitor },
      { key: 'phone', icon: Phone },
      { key: 'mail', icon: Message }
    ]
  },
  // 自定义验证规则
  customRules: {
    type: Object,
    default: () => ({})
  }
})

// 定义事件
const emit = defineEmits(['login', 'forgot-password', 'register', 'other-login'])

// 表单引用
const loginFormRef = ref(null)

// 加载状态
const loading = ref(false)

// 登录表单数据
const loginForm = reactive({
  username: '',
  password: '',
  remember: false
})

// 根据用户名类型计算图标
const userIcon = computed(() => {
  switch (props.usernameType) {
    case 'phone':
      return Phone
    case 'mail':
      return Message
    default:
      return User
  }
})

// 密码图标
const passwordIcon = Lock

// 节流函数
const throttle = (fn, delay) => {
  let timer = null
  return function(...args) {
    if (!timer) {
      timer = setTimeout(() => {
        fn.apply(this, args)
        timer = null
      }, delay)
    }
  }
}

// 登录验证规则
const rules = reactive({
  username: [
    { required: true, message: props.usernamePlaceholder || '请输入用户名/邮箱', trigger: ['blur', 'input'] },
    { min: 3, max: 10, message: '用户名长度必须在3-10个字符之间', trigger: ['blur', 'input'] },
    ...(props.customRules.username || [])
  ],
  password: [
    { required: true, message: '请输入密码', trigger: ['blur', 'input'] },
    { min: 6, message: '密码长度不能少于6个字符', trigger: ['blur', 'input'] },
    ...(props.customRules.password || [])
  ]
})

// 实时验证处理函数
const handleInput = throttle(async (field) => {
  if (loginFormRef.value) {
    await loginFormRef.value.validateField(field)
  }
}, 300)

// 初始化，尝试从本地存储加载记住的密码
onMounted(() => {
  const savedUsername = localStorage.getItem('rememberedUsername')
  if (savedUsername) {
    loginForm.username = savedUsername
    loginForm.remember = true
  }
})

// 处理登录
const handleLogin = async () => {
  try {
    // 表单验证
    await loginFormRef.value.validate()
    
    // 设置加载状态
    loading.value = true
    
    // 提交登录事件给父组件
    emit('login', { ...loginForm })
    
    // 如果选择记住密码，保存用户名到本地存储
    if (loginForm.remember) {
      localStorage.setItem('rememberedUsername', loginForm.username)
    } else {
      localStorage.removeItem('rememberedUsername')
    }
    
  } catch (error) {
    console.error('登录表单验证失败:', error)
  } finally {
    // 重置加载状态
    loading.value = false
  }
}

// 处理忘记密码
const handleForgotPassword = () => {
  emit('forgot-password')
}

// 处理注册
const handleRegister = () => {
  emit('register')
}

// 处理其他登录方式
const handleOtherLogin = (method) => {
  emit('other-login', method)
}

// 重置表单
const resetForm = () => {
  loginFormRef.value.resetFields()
}

// 设置表单值
const setFormValues = (values) => {
  Object.assign(loginForm, values)
}

// 暴露方法给父组件
defineExpose({
  handleLogin,
  resetForm,
  setFormValues,
  loginFormRef
})
</script>

<style scoped>
.login-form-wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  min-height: 100vh;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  padding: 0;
  margin: 0;
  overflow: hidden;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: 1;
}

.login-form-container {
  width: 100%;
  max-width: 360px;
  background: #fff;
  border-radius: 12px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
  overflow: hidden;
  animation: fadeIn 0.3s ease-in-out;
  box-sizing: border-box;
}

.login-form-header {
  padding: 16px 16px 0;
  text-align: center;
}

.login-form-header h2 {
  margin: 0 0 8px;
  color: #303133;
  font-size: 20px;
  font-weight: 600;
}

.login-form-header p {
  margin: 0;
  color: #606266;
  font-size: 14px;
}

.login-form {
  padding: 16px;
}

.login-form .el-form-item {
  margin-bottom: 16px;
}

.login-form .el-input {
  height: 40px;
}

.login-form .el-input__wrapper {
  border-radius: 8px;
}

.login-form-options {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
  font-size: 14px;
}

.login-form-options .el-checkbox__label {
  color: #606266;
}

.login-btn {
  width: 100%;
  height: 36px;
  border-radius: 8px;
  font-size: 15px;
  font-weight: 500;
  transition: all 0.3s ease;
}

.login-btn:hover {
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(64, 158, 255, 0.3);
}

.login-form-other {
  margin-top: 16px;
}

.login-form-other-divider {
  display: flex;
  align-items: center;
  margin-bottom: 12px;
  color: #909399;
  font-size: 12px;
}

.login-form-other-divider::before,
.login-form-other-divider::after {
  content: '';
  flex: 1;
  height: 1px;
  background: #ebeef5;
}

.login-form-other-divider span {
  padding: 0 10px;
}

.login-form-other-methods {
  display: flex;
  justify-content: center;
  gap: 20px;
}

.login-icon {
  font-size: 24px;
  color: #606266;
  cursor: pointer;
  transition: color 0.3s ease;
}

.login-icon:hover {
  color: #409eff;
}

.login-form-register {
  text-align: center;
  margin-top: 12px;
  font-size: 14px;
  color: #606266;
}

.login-form-register .el-link {
  margin-left: 5px;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(-20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* 响应式设计 */
@media (max-width: 480px) {
  .login-form-container {
    max-width: 100%;
    box-shadow: none;
    border-radius: 8px;
  }
  
  .login-form-header,
  .login-form {
    padding: 20px;
  }
}
</style>