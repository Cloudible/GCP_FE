<template>
  <div class="container">
    <div class="content-wrapper">
      <!-- 성공 상태 -->
      <div class="success-content">
        <!-- 성공 메시지 -->
        <div class="success-card">
          <div class="success-icon">
            <CheckCircle class="w-8 h-8" />
          </div>
          <h1>로그인 성공!</h1>
          <p>구글 계정으로 성공적으로 인증되었습니다</p>
          <div class="success-animation"></div>
        </div>

        <!-- 사용자 정보 -->
        <div v-if="guildNm" class="info-card guild-card">
          <div class="card-header">
            <div class="header-icon">
              <Hash class="w-5 h-5" />
            </div>
            <h2>채널 정보</h2>
          </div>
          <div class="card-content">
            <div class="guild-info">
              <div class="guild-avatar">
                <Hash class="w-6 h-6" />
              </div>
              <div class="guild-details">
                <h3>{{ guildNm }}</h3>
              </div>
            </div>
          </div>
        </div>

        <!-- 계정 정보 -->
        <div v-if="userNm" class="info-card user-card">
          <div class="card-header">
            <div class="header-icon">
              <User class="w-5 h-5" />
            </div>
            <h2>계정 정보</h2>
          </div>
          <div class="card-content">
            <div class="user-info">
              <div class="avatar">
                <img v-if="userImage" :src="userImage" alt="User Avatar" />
                <User v-else class="w-8 h-8" />
              </div>
              <div class="user-details">
                <h3>{{ userNm }}</h3>
                <div class="user-meta">
                  <div class="login-time">
                    <Calendar class="w-4 h-4" />
                    <span>{{ formatDate(loginTime) }}</span>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- 자동 리다이렉트 정보 -->
        <div class="redirect-card">
          <div class="redirect-content">
            <Clock class="w-5 h-5" />
            <div class="redirect-text">
              <p>{{ redirectCountdown }}초 후 자동으로 창이 닫힙니다</p>
              <div class="progress-bar">
                <div
                  class="progress-fill"
                  :style="{ width: `${((5 - redirectCountdown) / 5) * 100}%` }"
                ></div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import { CheckCircle, Hash, User, Calendar, Clock } from 'lucide-vue-next'

const redirectCountdown = ref(5)
const loginTime = ref(new Date())
let countdownTimer = null

const userNm = ref('')
const guildNm = ref('')
const userImage = ref('')

const encoded = new URLSearchParams(window.location.search).get('info')
if (encoded) {
  const decoded = decodeURIComponent(escape(atob(encoded)))
  const [userName, guildName, userProfile] = decoded.split('|')
  userNm.value = userName
  guildNm.value = guildName
  userImage.value = userProfile
  console.log(userImage.value)
}

onMounted(() => {
  startCountdown()
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
  self.opener = self
  window.close()
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

.success-content {
  display: flex;
  flex-direction: column;
  gap: 20px;
  animation: slideUp 0.6s ease-out;
}

@keyframes slideUp {
  from {
    opacity: 0;
    transform: translateY(30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.success-card {
  background: rgba(255, 255, 255, 0.98);
  backdrop-filter: blur(20px);
  border-radius: 20px;
  padding: 40px 32px;
  text-align: center;
  box-shadow: 0 25px 50px rgba(0, 0, 0, 0.15);
  border: 1px solid rgba(255, 255, 255, 0.3);
  position: relative;
  overflow: hidden;
}

.success-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 4px;
  background: linear-gradient(90deg, #10b981, #059669);
}

.success-icon {
  width: 80px;
  height: 80px;
  background: linear-gradient(135deg, #d1fae5, #a7f3d0);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 0 auto 24px;
  color: #059669;
  animation: pulse 2s infinite;
}

@keyframes pulse {
  0%,
  100% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.05);
  }
}

.success-animation {
  position: absolute;
  top: -50%;
  left: -50%;
  width: 200%;
  height: 200%;
  background: radial-gradient(circle, rgba(16, 185, 129, 0.1) 0%, transparent 70%);
  animation: rotate 10s linear infinite;
  pointer-events: none;
}

@keyframes rotate {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

.success-card h1 {
  font-size: 32px;
  font-weight: 800;
  background: linear-gradient(135deg, #059669, #10b981);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  margin: 0 0 12px 0;
}

.success-card p {
  color: #065f46;
  margin: 0;
  font-size: 16px;
  font-weight: 500;
}

.info-card {
  background: rgba(255, 255, 255, 0.98);
  backdrop-filter: blur(20px);
  border-radius: 20px;
  padding: 0;
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.1);
  border: 1px solid rgba(255, 255, 255, 0.3);
  overflow: hidden;
  transition:
    transform 0.2s ease,
    box-shadow 0.2s ease;
}

.info-card:hover {
  transform: translateY(-2px);
  box-shadow: 0 25px 50px rgba(0, 0, 0, 0.15);
}

.card-header {
  background: linear-gradient(135deg, #f8fafc, #f1f5f9);
  padding: 20px 24px;
  border-bottom: 1px solid rgba(226, 232, 240, 0.8);
  display: flex;
  align-items: center;
  gap: 12px;
}

.header-icon {
  width: 40px;
  height: 40px;
  background: linear-gradient(135deg, #3b82f6, #1d4ed8);
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
}

.guild-card .header-icon {
  background: linear-gradient(135deg, #8b5cf6, #7c3aed);
}

.card-header h2 {
  font-size: 18px;
  font-weight: 700;
  margin: 0;
  color: #1e293b;
}

.card-content {
  padding: 24px;
}

.guild-info {
  display: flex;
  align-items: center;
  gap: 16px;
}

.guild-avatar {
  width: 56px;
  height: 56px;
  background: linear-gradient(135deg, #ddd6fe, #c4b5fd);
  border-radius: 16px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #7c3aed;
}

.guild-details h3 {
  font-size: 20px;
  font-weight: 700;
  margin: 0 0 4px 0;
  color: #1e293b;
}

.guild-status {
  color: #10b981;
  font-size: 14px;
  font-weight: 600;
  background: rgba(16, 185, 129, 0.1);
  padding: 4px 12px;
  border-radius: 20px;
}

.user-info {
  display: flex;
  gap: 20px;
  align-items: flex-start;
}

.avatar {
  width: 72px;
  height: 72px;
  border-radius: 20px;
  overflow: hidden;
  background: linear-gradient(135deg, #dbeafe, #bfdbfe);
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  color: #3b82f6;
  border: 3px solid rgba(59, 130, 246, 0.2);
}

.avatar img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.user-details {
  flex: 1;
}

.user-details h3 {
  font-size: 22px;
  font-weight: 700;
  margin: 0 0 12px 0;
  color: #1e293b;
}

.user-meta {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.login-time {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 14px;
  color: #64748b;
  font-weight: 500;
}

.status-badge {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 14px;
  font-weight: 600;
  color: #10b981;
}

@keyframes pulse-dot {
  0%,
  100% {
    opacity: 1;
  }
  50% {
    opacity: 0.5;
  }
}

.redirect-card {
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(20px);
  border-radius: 16px;
  padding: 20px;
  box-shadow: 0 15px 30px rgba(0, 0, 0, 0.1);
  border: 1px solid rgba(255, 255, 255, 0.3);
}

.redirect-content {
  display: flex;
  align-items: center;
  gap: 16px;
  color: #475569;
}

.redirect-content svg {
  color: #3b82f6;
  flex-shrink: 0;
}

.redirect-text {
  flex: 1;
}

.redirect-text p {
  margin: 0 0 8px 0;
  font-size: 14px;
  font-weight: 600;
}

.progress-bar {
  width: 100%;
  height: 4px;
  background: rgba(59, 130, 246, 0.2);
  border-radius: 2px;
  overflow: hidden;
}

.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, #3b82f6, #1d4ed8);
  border-radius: 2px;
  transition: width 1s linear;
}

/* 반응형 */
@media (max-width: 480px) {
  .container {
    padding: 16px;
  }

  .success-card {
    padding: 32px 24px;
  }

  .success-card h1 {
    font-size: 28px;
  }

  .card-header {
    padding: 16px 20px;
  }

  .card-content {
    padding: 20px;
  }

  .user-info {
    flex-direction: column;
    text-align: center;
    align-items: center;
  }

  .guild-info {
    flex-direction: column;
    text-align: center;
  }

  .user-meta {
    align-items: center;
  }
}
</style>
