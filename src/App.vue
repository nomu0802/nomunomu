<script setup>
import { ref, onMounted, onUnmounted, computed } from 'vue'

// 時刻を保持するリアクティブな変数
const currentTime = ref(new Date())
let timerId = null

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
  
  return {
    time: `${String(hours).padStart(2, '0')}:${minutes}:${seconds}`,
    ampm: ampm
  }
})

// ドメイン名を表示するための変数
const domainName = 'nomunomu.dev'

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
</script>

<template>
  <div class="clock-container">
    <div class="time-display">
      <span class="time-main">{{ formattedTime.time }}</span>
      
      <span class="time-ampm">{{ formattedTime.ampm }}</span>
    </div>
    
    <p class="domain-name">{{ domainName }}</p>
  </div>
</template>

<style scoped>
/* 画面全体を占有し、背景を黒くする */
.clock-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh; /* ビューポートの高さ全体 */
  width: 100vw;  /* ビューポートの幅全体 */
  background-color: #121212; /* 濃いグレー/黒 */
  color: #00ff00; /* ネオングリーンのような色 */
  font-family: 'Digital', monospace; /* デジタルなフォントを推奨 */
}

/* 時刻表示エリア */
.time-display {
  display: flex;
  align-items: flex-end; /* AM/PMをメイン時刻の下端に揃える */
  margin-bottom: 20px;
}

/* HH:MM:SS のメイン表示 */
.time-main {
  font-size: 10vw; /* ビューポート幅に対する相対的なサイズで大きく */
  font-weight: bold;
  letter-spacing: -5px; /* 数字間の間隔を詰める */
  line-height: 1;
}

/* AM/PM 表示 */
.time-ampm {
  font-size: 3vw; /* メイン時刻に比べて小さく */
  margin-left: 20px;
  transform: translateY(-5px); /* 微調整して垂直位置を合わせる */
  line-height: 1;
}

/* ドメイン名表示 */
.domain-name {
  font-size: 1.5vw;
  color: #33a533; /* メイン時刻より少し暗い色 */
  letter-spacing: 5px;
  opacity: 0.8;
  margin-top: -10px; /* 時刻との間隔を詰める */
}

/* 全体のスタイル（必要に応じて src/style.css や index.html に追加） */
/* bodyの余白をなくすためのグローバルスタイル（App.vueの外に適用推奨） */
/* index.css（またはmain.js/App.vueでグローバルに）に追加:
   body { margin: 0; padding: 0; overflow: hidden; } 
*/
</style>