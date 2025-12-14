<script setup>
import { ref, onMounted, onUnmounted, computed } from 'vue'

// 時刻を保持するリアクティブな変数
const currentTime = ref(new Date())
let timerId = null

// ダークモード/ライトモードの切り替え
const isDarkMode = ref(true)

// 時刻を HH:MM:SS の形式でフォーマットする算出プロパティ
const formattedTime = computed(() => {
  // 現在の時刻を取得
  const now = currentTime.value
  
  // 12時間形式（1桁の場合は0パディング）
  let hours = now.getHours()
  const ampm = hours >= 12 ? 'PM' : 'AM'
  hours = hours % 12
  hours = hours ? hours : 12; // 0時は12時として扱う

  // 分と秒（常に2桁に0パディング）
  const minutes = String(now.getMinutes()).padStart(2, '0')
  const seconds = String(now.getSeconds()).padStart(2, '0')
  
  // 日付のフォーマット
  const year = now.getFullYear()
  const month = String(now.getMonth() + 1).padStart(2, '0')
  const date = String(now.getDate()).padStart(2, '0')
  const weekdays = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday']
  const weekday = weekdays[now.getDay()]
  
  return {
    time: `${String(hours).padStart(2, '0')}:${minutes}:${seconds}`,
    ampm: ampm,
    date: `${year}.${month}.${date}`,
    weekday: weekday
  }
})

// ドメイン名を表示するための変数
const domainName = 'nomunomu.dev'

// SNSリンク
const socialLinks = [
  { name: 'GitHub', url: 'https://github.com/nommmmu', icon: 'GH' },
  { name: 'X', url: 'https://x.com/nommmmmu', icon: '𝕏' },
]

// 1秒ごとに時間を更新する関数
const updateTime = () => {
  currentTime.value = new Date()
}

// コンポーネントがマウントされたときにタイマーを開始
onMounted(() => {
  // 1000ミリ秒（1秒）ごとに updateTime を実行
  timerId = setInterval(updateTime, 1000)
})

// コンポーネントがアンマウントされたときにタイマーをクリア
onUnmounted(() => {
  if (timerId) {
    clearInterval(timerId)
  }
})

// テーマカラーの算出プロパティ
const themeColors = computed(() => {
  if (isDarkMode.value) {
    return {
      background: '#121212',
      text: '#65D6AD',
      buttonBg: '#121212',
      buttonBorder: '#65D6AD',
      buttonText: '#65D6AD'
    }
  } else {
    return {
      background: '#f5f5f5',
      text: '#014D40',
      buttonBg: 'rgba(51, 51, 51, 0.1)',
      buttonBorder: '#014D40',
      buttonText: '#014D40'
    }
  }
})

// モード切り替え関数
const toggleTheme = () => {
  isDarkMode.value = !isDarkMode.value
}
</script>

<template>
  <div 
    class="clock-container" 
    :style="{ 
      backgroundColor: themeColors.background, 
      color: themeColors.text 
    }"
  >
    <button 
      class="theme-toggle" 
      @click="toggleTheme"
      :style="{
        backgroundColor: themeColors.buttonBg,
        borderColor: themeColors.buttonBorder,
        color: themeColors.buttonText
      }"
    >
      <span class="icon">
        <!-- ダークモード時は太陽アイコン -->
        <svg v-if="isDarkMode" viewBox="0 0 24 24" width="24" height="24" fill="none" stroke="currentColor" stroke-width="2">
          <circle cx="12" cy="12" r="5"/>
          <line x1="12" y1="1" x2="12" y2="3"/>
          <line x1="12" y1="21" x2="12" y2="23"/>
          <line x1="4.22" y1="4.22" x2="5.64" y2="5.64"/>
          <line x1="18.36" y1="18.36" x2="19.78" y2="19.78"/>
          <line x1="1" y1="12" x2="3" y2="12"/>
          <line x1="21" y1="12" x2="23" y2="12"/>
          <line x1="4.22" y1="19.78" x2="5.64" y2="18.36"/>
          <line x1="18.36" y1="5.64" x2="19.78" y2="4.22"/>
        </svg>
        <!-- ライトモード時は月アイコン -->
        <svg v-else viewBox="0 0 24 24" width="24" height="24" fill="currentColor">
          <path d="M21 12.79A9 9 0 1 1 11.21 3 7 7 0 0 0 21 12.79z"/>
        </svg>
      </span>
    </button>
    
    <div class="date-display">
      <span class="date-text">{{ formattedTime.date }}</span>
      <span class="weekday-text">{{ formattedTime.weekday }}</span>
    </div>
    
    <div class="time-display">
      <span class="time-main">{{ formattedTime.time }}</span>
      
      <span class="time-ampm">{{ formattedTime.ampm }}</span>
    </div>
    
    <p class="domain-name">{{ domainName }}</p>
    
    <div class="social-links">
      <a 
        v-for="link in socialLinks" 
        :key="link.name"
        :href="link.url" 
        target="_blank"
        rel="noopener noreferrer"
        class="social-link"
        :style="{ color: themeColors.text }"
        :title="link.name"
      >
        <span v-if="link.name === 'GitHub'" class="social-icon">
          <svg viewBox="0 0 16 16" width="20" height="20" fill="currentColor">
            <path d="M8 0C3.58 0 0 3.58 0 8c0 3.54 2.29 6.53 5.47 7.59.4.07.55-.17.55-.38 
              0-.19-.01-.82-.01-1.49-2.01.37-2.53-.49-2.69-.94-.09-.23-.48-.94-.82-1.13-.28-.15-.68-.52-.01-.53.63-.01 
              1.08.58 1.23.82.72 1.21 1.87.87 2.33.66.07-.52.28-.87.51-1.07-1.78-.2-3.64-.89-3.64-3.95 
              0-.87.31-1.59.82-2.15-.08-.2-.36-1.02.08-2.12 0 0 .67-.21 2.2.82.64-.18 1.32-.27 2-.27.68 0 
              1.36.09 2 .27 1.53-1.04 2.2-.82 2.2-.82.44 1.1.16 1.92.08 2.12.51.56.82 1.27.82 2.15 0 
              3.07-1.87 3.75-3.65 3.95.29.25.54.73.54 1.48 0 1.07-.01 1.93-.01 2.2 0 .21.15.46.55.38A8.013 
              8.013 0 0016 8c0-4.42-3.58-8-8-8z"/>
          </svg>
        </span>
        <span v-else class="social-icon">{{ link.icon }}</span>
      </a>
    </div>
  </div>
</template>
