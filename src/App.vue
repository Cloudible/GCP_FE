<template>
  <div class="container">
    <div class="content-wrapper">
      <!-- 로딩 상태 -->
      <div v-if="isLoading" class="card loading-card">
        <div class="loading-content">
          <div class="spinner"></div>
          <p>인증 정보를 확인하는 중...</p>
        </div>
      </div>

      <!-- 성공 상태 -->
      <div v-else class="success-content">
        <!-- 성공 메시지 -->
        <div class="success-card">
          <div class="success-icon">
            <CheckCircleIcon />
          </div>
          <h1>로그인 성공!</h1>
          <p>구글 계정으로 성공적으로 인증되었습니다</p>
        </div>

        <!-- 사용자 정보 -->
        <div v-if="userInfo" class="user-card">
          <h2>계정 정보</h2>
          <div class="user-info">
            <div class="avatar">
              <img v-if="userInfo.picture" :src="userInfo.picture" :alt="userInfo.name" />
              <UserIcon v-else />
            </div>
            <div class="user-details">
              <h3>{{ userInfo.name }}</h3>
              <div class="email-info">
                <MailIcon />
                <span>{{ userInfo.email }}</span>
                <span v-if="userInfo.verified_email" class="verified-badge">인증됨</span>
              </div>
              <div class="login-time">
                <CalendarIcon />
                <span>로그인: {{ formatDate(loginTime) }}</span>
              </div>
            </div>
          </div>
        </div>

        <!-- 액션 버튼들 -->
        <div class="actions-card">
          <h2>다음 단계</h2>
          <p>계정 설정을 완료하거나 바로 시작하세요</p>
          <div class="buttons">
            <button @click="handleContinue" class="primary-button">
              <ArrowRightIcon />
              대시보드로 이동
            </button>
            <button @click="handleProfileSetup" class="secondary-button">프로필 설정하기</button>
          </div>
        </div>

        <!-- 자동 리다이렉션 안내 -->
        <div class="redirect-info">
          <p v-if="redirectCountdown > 0">
            {{ redirectCountdown }}초 후 자동으로 대시보드로 이동합니다
          </p>
          <p v-else>리다이렉션 중...</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import { CheckCircleIcon, UserIcon, MailIcon, CalendarIcon, ArrowRightIcon } from 'lucide-vue-next'

const userInfo = ref(null)
const isLoading = ref(true)
const redirectCountdown = ref(5)
const loginTime = ref(new Date())
let countdownTimer = null

onMounted(() => {
  const mockUserInfo = {
    name: '김철수',
    email: 'kimcs@gmail.com',
    picture: '/placeholder.svg?height=80&width=80&text=Profile',
    verified_email: true,
  }

  setTimeout(() => {
    userInfo.value = mockUserInfo
    isLoading.value = false
    startCountdown()
  }, 1000)
})

const startCountdown = () => {
  countdownTimer = setInterval(() => {
    if (redirectCountdown.value > 0) {
      redirectCountdown.value--
    } else {
      clearInterval(countdownTimer)
      handleAutoRedirect()
    }
  }, 1000)
}

const handleAutoRedirect = () => {
  console.log('Auto redirecting to dashboard...')
}

const handleContinue = () => {
  clearInterval(countdownTimer)
  console.log('Navigating to dashboard...')
}

const handleProfileSetup = () => {
  clearInterval(countdownTimer)
  console.log('Navigating to profile setup...')
}

const formatDate = (date) => {
  return date.toLocaleString('ko-KR', {
    month: '2-digit',
    day: '2-digit',
    hour: '2-digit',
    minute: '2-digit',
  })
}

onUnmounted(() => {
  if (countdownTimer) {
    clearInterval(countdownTimer)
  }
})
</script>

<style scoped>
* {
  box-sizing: border-box;
}

.container {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 20px;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
}

.content-wrapper {
  width: 100%;
  max-width: 420px;
}

.card {
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(10px);
  border-radius: 16px;
  padding: 32px;
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.1);
  border: 1px solid rgba(255, 255, 255, 0.2);
}

.loading-card {
  text-align: center;
}

.loading-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 20px;
}

.spinner {
  width: 40px;
  height: 40px;
  border: 3px solid #f3f3f3;
  border-top: 3px solid #667eea;
  border-radius: 50%;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

.success-content {
  display: flex;
  flex-direction: column;
  gap: 24px;
}

.success-card {
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(10px);
  border-radius: 16px;
  padding: 32px;
  text-align: center;
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.1);
  border: 1px solid rgba(34, 197, 94, 0.2);
}

.success-icon {
  width: 64px;
  height: 64px;
  background: #dcfce7;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 0 auto 20px;
}

.success-icon svg {
  width: 32px;
  height: 32px;
  color: #16a34a;
}

.success-card h1 {
  font-size: 28px;
  font-weight: 700;
  color: #16a34a;
  margin: 0 0 8px 0;
}

.success-card p {
  color: #059669;
  margin: 0;
  font-size: 16px;
}

.user-card {
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(10px);
  border-radius: 16px;
  padding: 24px;
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.1);
  border: 1px solid rgba(255, 255, 255, 0.2);
}

.user-card h2 {
  font-size: 20px;
  font-weight: 600;
  margin: 0 0 20px 0;
  color: #1f2937;
}

.user-info {
  display: flex;
  gap: 16px;
  align-items: flex-start;
}

.avatar {
  width: 64px;
  height: 64px;
  border-radius: 50%;
  overflow: hidden;
  background: #f3f4f6;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
}

.avatar img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.avatar svg {
  width: 32px;
  height: 32px;
  color: #6b7280;
}

.user-details {
  flex: 1;
}

.user-details h3 {
  font-size: 20px;
  font-weight: 600;
  margin: 0 0 8px 0;
  color: #1f2937;
}

.email-info,
.login-time {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 8px;
  font-size: 14px;
  color: #6b7280;
}

.email-info svg,
.login-time svg {
  width: 16px;
  height: 16px;
}

.verified-badge {
  background: #f3f4f6;
  color: #374151;
  padding: 2px 8px;
  border-radius: 12px;
  font-size: 12px;
  font-weight: 500;
}

.actions-card {
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(10px);
  border-radius: 16px;
  padding: 24px;
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.1);
  border: 1px solid rgba(255, 255, 255, 0.2);
}

.actions-card h2 {
  font-size: 20px;
  font-weight: 600;
  margin: 0 0 8px 0;
  color: #1f2937;
}

.actions-card p {
  color: #6b7280;
  margin: 0 0 20px 0;
  font-size: 14px;
}

.buttons {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.primary-button {
  background: #1f2937;
  color: white;
  border: none;
  padding: 16px 24px;
  border-radius: 12px;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  transition: all 0.2s ease;
}

.primary-button:hover {
  background: #374151;
  transform: translateY(-1px);
}

.primary-button svg {
  width: 20px;
  height: 20px;
}

.secondary-button {
  background: transparent;
  color: #374151;
  border: 2px solid #e5e7eb;
  padding: 14px 24px;
  border-radius: 12px;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.2s ease;
}

.secondary-button:hover {
  background: #f9fafb;
  border-color: #d1d5db;
  transform: translateY(-1px);
}

.redirect-info {
  text-align: center;
  color: rgba(255, 255, 255, 0.8);
  font-size: 14px;
}

.redirect-info p {
  margin: 0;
}

/* 반응형 */
@media (max-width: 480px) {
  .container {
    padding: 16px;
  }

  .card,
  .success-card,
  .user-card,
  .actions-card {
    padding: 20px;
  }

  .success-card h1 {
    font-size: 24px;
  }

  .user-info {
    flex-direction: column;
    text-align: center;
  }

  .avatar {
    align-self: center;
  }
}
</style>
