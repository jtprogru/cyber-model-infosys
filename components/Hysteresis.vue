<script setup lang="ts">
import { computed, useId } from 'vue'

// Шкала нагрузки с гистерезисом: до порога система стабильна, выше — уязвима.
// Маркер показывает, где её роняет триггер, пунктирная стрелка — что выйти из
// отказа можно только вернувшись ниже порога.
//
// Позиции порога и триггера считаются от max. Ширина W — контентная область
// слайда темы: холст 980px минус поля px-16.
interface Zone { title: string, note: string }

const props = withDefaults(defineProps<{
  max: number
  threshold: number
  trigger: number
  unit?: string
  stable: Zone
  vulnerable: Zone
  triggerLabel: string
  exitLabel: string
}>(), {
  unit: '',
})

const W = 852
const TRACK_Y = 14
const TRACK_H = 14
const TICKS_Y = 52
const EXIT_Y = 82
const H = 106

const thresholdX = computed(() => W * props.threshold / props.max)
const triggerX = computed(() => W * props.trigger / props.max)

const id = useId()
const arrow = `hyst-arrow-${id}`
</script>

<template>
  <div class="hyst mx-auto" :style="{ width: `${W}px` }">
    <div class="grid grid-cols-2 gap-8 font-mono text-xs mb-3">
      <div class="hyst__zone">
        <b>{{ props.stable.title }}</b><br>{{ props.stable.note }}
      </div>
      <div class="hyst__zone hyst__zone--vulnerable text-right">
        <b>{{ props.vulnerable.title }}</b><br>{{ props.vulnerable.note }}
      </div>
    </div>

    <div class="relative" :style="{ height: `${H}px` }">
      <svg class="absolute inset-0" :width="W" :height="H" :viewBox="`0 0 ${W} ${H}`" aria-hidden="true">
        <defs>
          <marker :id="arrow" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto">
            <path d="M0,0 L10,5 L0,10 z" class="hyst__head" />
          </marker>
        </defs>
        <rect :y="TRACK_Y" :width="W" :height="TRACK_H" :rx="TRACK_H / 2" class="hyst__track" />
        <!-- левый край зоны прямой: она начинается с порога, а не со скругления -->
        <path
          :d="`M${thresholdX},${TRACK_Y} H${W - TRACK_H / 2} a${TRACK_H / 2},${TRACK_H / 2} 0 0 1 0,${TRACK_H} H${thresholdX} z`"
          class="hyst__vulnerable"
        />
        <rect :x="thresholdX - 1" :y="TRACK_Y - 8" width="2" :height="TRACK_H + 16" class="hyst__mark" />
        <rect :x="triggerX - 2" :y="0" width="4" :height="TRACK_Y * 2 + TRACK_H" rx="2" class="hyst__mark" />
        <path
          :d="`M${triggerX},${EXIT_Y} H${thresholdX + 2}`"
          class="hyst__exit"
          :marker-end="`url(#${arrow})`"
        />
      </svg>

      <div class="hyst__tick font-mono text-xs" :style="{ left: 0, top: `${TICKS_Y}px` }">
        0
      </div>
      <div
        class="hyst__tick hyst__tick--accent font-mono text-xs"
        :style="{ left: `${thresholdX}px`, top: `${TICKS_Y}px`, transform: 'translateX(-50%)' }"
      >
        {{ props.threshold }}{{ props.unit }}
      </div>
      <div
        class="hyst__tick hyst__tick--accent font-mono text-xs font-bold"
        :style="{ right: `${W - triggerX - 2}px`, top: `${TICKS_Y}px` }"
      >
        {{ props.triggerLabel }}
      </div>
      <div
        class="hyst__tick text-sm text-center"
        :style="{ left: `${thresholdX}px`, width: `${triggerX - thresholdX}px`, top: `${EXIT_Y + 8}px` }"
      >
        {{ props.exitLabel }}
      </div>
    </div>
  </div>
</template>

<style scoped>
.hyst {
  --loop: var(--accent-700);
}

html.dark .hyst {
  --loop: var(--accent-300);
}

.hyst__zone {
  line-height: 1.5;
  color: var(--fg-muted);
}

.hyst__zone--vulnerable {
  color: var(--loop);
}

.hyst__track {
  fill: var(--border);
}

.hyst__vulnerable {
  fill: color-mix(in srgb, var(--accent-400) 55%, var(--border));
}

.hyst__mark {
  fill: var(--loop);
}

.hyst__tick {
  position: absolute;
  line-height: 1;
  white-space: nowrap;
  color: var(--fg-muted);
}

.hyst__tick--accent {
  color: var(--loop);
}

.hyst__exit {
  fill: none;
  stroke: var(--loop);
  stroke-width: 2;
  stroke-dasharray: 7 6;
  animation: hyst-march 1.2s linear infinite;
}

.hyst__head {
  fill: var(--loop);
}

@keyframes hyst-march {
  to {
    stroke-dashoffset: -13;
  }
}

@media (prefers-reduced-motion: reduce) {
  .hyst__exit {
    animation: none;
  }
}
</style>
