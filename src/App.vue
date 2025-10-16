<template>
  <login-form
    ref="loginFormRef"
    title="用户登录"
    subtitle="欢迎回来，请登录您的账号"
    username-type="user"
    :show-other-login-methods="true"
    @login="handleLogin"
    @forgot-password="handleForgotPassword"
    @register="handleRegister"
    @other-login="handleOtherLogin"
  />
</template>

<script setup>
import { ref } from 'vue'
import LoginForm from './components/LoginForm.vue'

// 登录表单引用
const loginFormRef = ref(null)

// 处理登录
const handleLogin = async (formData) => {
  console.log('登录表单数据:', formData)
  
  // 模拟登录请求
  try {
    // 这里可以替换为实际的登录API调用
    await new Promise(resolve => setTimeout(resolve, 1000))
    
    console.log('登录成功！')
    // 登录成功后的逻辑，如跳转到首页
    alert(`登录成功！\n用户名: ${formData.username}`)
  } catch (error) {
    console.error('登录失败:', error)
    alert('登录失败，请检查您的用户名和密码')
  }
}

// 处理忘记密码
const handleForgotPassword = () => {
  console.log('忘记密码')
  alert('忘记密码功能即将上线')
}

// 处理注册
const handleRegister = () => {
  console.log('注册账号')
  alert('注册功能即将上线')
}

// 处理其他登录方式
const handleOtherLogin = (method) => {
  console.log('其他登录方式:', method)
  alert(`第三方登录功能 (${method.key}) 即将上线`)
}
</script>

<style>
/* 全局样式重置 */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', 'Oxygen',
    'Ubuntu', 'Cantarell', 'Fira Sans', 'Droid Sans', 'Helvetica Neue',
    sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  background-color: #f5f7fa;
}

#app {
  width: 100%;
  height: 100vh;
}
</style>
