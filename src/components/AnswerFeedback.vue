<script setup>
defineProps({
  result: Object,
  isGameOver: Boolean,
  isVictory: Boolean,
})
defineEmits(['next'])

function pluralYears(n) {
  if (n % 100 >= 11 && n % 100 <= 19) return 'років'
  const mod = n % 10
  if (mod === 1) return 'рік'
  if (mod >= 2 && mod <= 4) return 'роки'
  return 'років'
}

function getAccuracy(diff) {
  if (diff === 0) return { label: 'ТОЧНО', color: '#111' }
  if (diff <= 5) return { label: 'БЛИЗЬКО', color: '#555' }
  return { label: 'ДАЛЕКО', color: '#aaa' }
}
</script>

<template>
  <div :class="$style.overlay">
    <div :class="$style.sheet">
      <div :class="$style.accuracy" :style="{ color: getAccuracy(result.diff).color }">
        {{ getAccuracy(result.diff).label }}
      </div>

      <div :class="$style.correctBlock">
        <span :class="$style.correctLabel">Правильна відповідь</span>
        <span :class="$style.correctYear">{{ result.correctYear }}</span>
      </div>

      <div :class="$style.divider" />

      <div :class="$style.statsRow">
        <div :class="$style.stat">
          <span :class="$style.statValue">+{{ result.pointsGained }}</span>
          <span :class="$style.statLabel">очків</span>
        </div>
        <div :class="$style.stat">
          <span :class="$style.statValue">
            {{ result.diff === 0 ? '±0' : result.diff + ' ' + pluralYears(result.diff) }}
          </span>
          <span :class="$style.statLabel">відхилення</span>
        </div>
        <div :class="$style.stat">
          <span :class="[$style.statValue, result.livesLost > 0 ? $style.danger : '']">
            {{ result.livesLost > 0 ? '−1 ●' : '✓' }}
          </span>
          <span :class="$style.statLabel">{{ result.livesLost > 0 ? 'життя' : 'без втрат' }}</span>
        </div>
      </div>

      <button :class="$style.nextBtn" @click="$emit('next')">
        {{ isGameOver ? 'РЕЗУЛЬТАТИ' : isVictory ? 'ЗАВЕРШИТИ' : 'ДАЛІ →' }}
      </button>
    </div>
  </div>
</template>

<style module>
.overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: flex-end;
  justify-content: center;
  z-index: 200;
}

.sheet {
  background: #fff;
  width: 100%;
  max-width: 420px;
  padding: 32px 24px 40px;
  display: flex;
  flex-direction: column;
  gap: 20px;
  border-radius: 12px 12px 0 0;
}

.accuracy {
  font-size: 11px;
  font-weight: 700;
  letter-spacing: 0.15em;
}

.correctBlock {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.correctLabel {
  font-size: 12px;
  color: #aaa;
}

.correctYear {
  font-size: 48px;
  font-weight: 700;
  color: #111;
  line-height: 1;
  letter-spacing: -0.02em;
}

.divider {
  height: 1px;
  background: #f0f0f0;
}

.statsRow {
  display: flex;
  gap: 0;
}

.stat {
  flex: 1;
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.statValue {
  font-size: 16px;
  font-weight: 700;
  color: #111;
}

.danger {
  color: #999;
}

.statLabel {
  font-size: 11px;
  color: #aaa;
  letter-spacing: 0.05em;
}

.nextBtn {
  width: 100%;
  background: #111;
  color: #fff;
  border: none;
  border-radius: 4px;
  padding: 20px;
  font-size: 14px;
  font-weight: 700;
  font-family: inherit;
  letter-spacing: 0.12em;
  cursor: pointer;
  transition: opacity 0.1s;
  margin-top: 4px;
}

.nextBtn:active {
  opacity: 0.8;
}
</style>
