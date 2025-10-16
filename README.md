# Vue 3 登录表单组件

一个功能完整、高度可配置的 Vue 3 登录表单组件，基于 Element Plus 构建。支持用户名/邮箱/手机号登录、密码验证、记住密码、第三方登录等功能。

## 功能特性

- 支持多种用户名类型（用户名、邮箱、手机号）
- 实时表单验证（带节流优化）
- 记住密码功能
- 第三方登录支持
- 响应式设计，适配不同设备
- 高度可配置的样式和行为
- 支持自定义验证规则
- 键盘操作支持（回车登录）

## 安装

```bash
# 安装依赖
npm install element-plus @element-plus/icons-vue
```

## 基本使用

```vue
<template>
  <LoginForm
    :title="'欢迎登录'"
    :subtitle="'请输入您的账号和密码'"
    :username-type="'user'"
    :show-remember-password="true"
    :show-forgot-password="true"
    :show-register-link="true"
    :show-other-login-methods="true"
    @login="handleLogin"
    @forgot-password="handleForgotPassword"
    @register="handleRegister"
    @other-login="handleOtherLogin"
  />
</template>

<script setup>
import { LoginForm } from './components/LoginForm.vue'

// 处理登录
const handleLogin = (formData) => {
  console.log('登录数据:', formData)
  // 这里实现登录逻辑
}

// 处理忘记密码
const handleForgotPassword = () => {
  console.log('忘记密码')
  // 这里实现忘记密码逻辑
}

// 处理注册
const handleRegister = () => {
  console.log('注册')
  // 这里实现注册逻辑
}

// 处理第三方登录
const handleOtherLogin = (method) => {
  console.log('第三方登录:', method)
  // 这里实现第三方登录逻辑
}
</script>
```

## 配置选项

### Props

| 属性名 | 类型 | 默认值 | 说明 |
|-------|------|-------|------|
| title | String | '账号登录' | 登录表单标题 |
| subtitle | String | '' | 登录表单副标题 |
| loginButtonText | String | '登录' | 登录按钮文字 |
| usernamePlaceholder | String | '请输入用户名/邮箱' | 用户名输入框占位符 |
| usernameType | String | 'user' | 用户名类型：'user'、'phone'、'mail' |
| showRememberPassword | Boolean | true | 是否显示记住密码选项 |
| showForgotPassword | Boolean | true | 是否显示忘记密码链接 |
| showRegisterLink | Boolean | true | 是否显示注册链接 |
| showOtherLoginMethods | Boolean | false | 是否显示其他登录方式 |
| otherLoginMethods | Array | [{key: 'wechat', icon: Monitor}, {key: 'phone', icon: Phone}, {key: 'mail', icon: Message}] | 其他登录方式配置 |
| customRules | Object | {} | 自定义验证规则 |

### Events

| 事件名 | 参数 | 说明 |
|-------|------|------|
| login | formData: Object | 登录表单数据，包含username、password、remember |
| forgot-password | 无 | 点击忘记密码链接触发 |
| register | 无 | 点击注册链接触发 |
| other-login | method: Object | 点击第三方登录图标触发，返回登录方式对象 |

### 暴露方法

| 方法名 | 参数 | 说明 |
|-------|------|------|
| handleLogin | 无 | 触发登录验证和提交 |
| resetForm | 无 | 重置表单 |
| setFormValues | values: Object | 设置表单值 |

## 自定义验证规则

您可以通过 `customRules` 属性自定义验证规则：

```vue
<LoginForm
  :custom-rules="{
    username: [
      { pattern: /^[a-zA-Z0-9_]+$/, message: '用户名只能包含字母、数字和下划线', trigger: ['blur', 'input'] }
    ],
    password: [
      { pattern: /^(?=.*[A-Za-z])(?=.*\d)[A-Za-z\d]{6,}$/, message: '密码必须包含字母和数字', trigger: ['blur', 'input'] }
    ]
  }"
  @login="handleLogin"
/>
```

## 第三方登录配置

您可以自定义第三方登录方式：

```vue
<LoginForm
  :show-other-login-methods="true"
  :other-login-methods="[
    { key: 'wechat', icon: Wechat },
    { key: 'qq', icon: Qq },
    { key: 'weibo', icon: Weibo }
  ]"
  @other-login="handleOtherLogin"
/>
```

## 表单验证规则

1. 用户名：
   - 必填
   - 长度必须在3-10个字符之间
   - 支持自定义规则

2. 密码：
   - 必填
   - 长度不能少于6个字符
   - 支持自定义规则

## 开发和构建

```bash
# 安装依赖
npm install

# 启动开发服务器
npm run dev

# 构建生产版本
npm run build
```

## 许可证

MIT License
