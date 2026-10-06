<script setup lang="ts">
import { useId } from 'vue'

// Замкнутый контур управления: уставка → сравнение → регулятор → объект,
// от объекта пунктиром через датчики обратно на сравнение.
//
// Узлы — HTML поверх одного SVG со стрелками: текст в узлах переносится сам,
// а геометрия стрелок считается от констант ниже, без замеров DOM. Ширина W —
// контентная область слайда темы: холст 980px минус поля px-16.
interface Node { title: string, note: string }

const props = defineProps<{
  setpoint: Node
  compare: Node
  controller: Node
  plant: Node
  sensors: Node
  disturbance: string
  output: string
  feedback: string
}>()

const W = 852
const BOX_W = 186
const BOX_H = 76
const GAP = 36
const TOP = 44
const SENS_W = 260
const SENS_H = 64
const SENS_Y = 196
const H = SENS_Y + SENS_H

const row = [props.setpoint, props.compare, props.controller, props.plant]
const xs = row.map((_, i) => i * (BOX_W + GAP))
const mid = TOP + BOX_H / 2
const bottom = TOP + BOX_H

// обратная связь уходит из центра объекта и приходит в центр сравнения,
// датчики стоят посередине между ними
const plantX = xs[3] + BOX_W / 2
const compareX = xs[1] + BOX_W / 2
const sensX = (plantX + compareX) / 2 - SENS_W / 2
const sensMid = SENS_Y + SENS_H / 2
const labelY = (bottom + SENS_Y) / 2

const id = useId()
const solidArrow = `loop-solid-${id}`
const feedbackArrow = `loop-feedback-${id}`
const mutedArrow = `loop-muted-${id}`
</script>

<template>
  <div class="loop relative mx-auto" :style="{ width: `${W}px`, height: `${H}px` }">
    <svg class="absolute inset-0" :width="W" :height="H" :viewBox="`0 0 ${W} ${H}`" aria-hidden="true">
      <defs>
        <marker :id="solidArrow" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto">
          <path d="M0,0 L10,5 L0,10 z" class="loop__head" />
        </marker>
        <marker :id="feedbackArrow" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto">
          <path d="M0,0 L10,5 L0,10 z" class="loop__head" />
        </marker>
        <marker :id="mutedArrow" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto">
          <path d="M0,0 L10,5 L0,10 z" class="loop__head-muted" />
        </marker>
      </defs>

      <path
        v-for="x in xs.slice(0, 3)"
        :key="x"
        :d="`M${x + BOX_W + 2},${mid} H${x + BOX_W + GAP - 2}`"
        class="loop__line"
        :marker-end="`url(#${solidArrow})`"
      />

      <path :d="`M${plantX},20 V${TOP - 2}`" class="loop__line-muted" :marker-end="`url(#${mutedArrow})`" />

      <path
        :d="`M${plantX},${bottom + 2} V${sensMid} H${sensX + SENS_W + 2}`"
        class="loop__line loop__line--feedback"
        :marker-end="`url(#${feedbackArrow})`"
      />
      <path
        :d="`M${sensX - 2},${sensMid} H${compareX} V${bottom + 2}`"
        class="loop__line loop__line--feedback"
        :marker-end="`url(#${feedbackArrow})`"
      />
    </svg>

    <div
      v-for="(node, i) in row"
      :key="node.title"
      class="loop__node"
      :style="{ left: `${xs[i]}px`, top: `${TOP}px`, width: `${BOX_W}px`, height: `${BOX_H}px` }"
    >
      <div class="loop__title">
        {{ node.title }}
      </div>
      <div class="loop__note font-mono text-xs">
        {{ node.note }}
      </div>
    </div>

    <div
      class="loop__node"
      :style="{ left: `${sensX}px`, top: `${SENS_Y}px`, width: `${SENS_W}px`, height: `${SENS_H}px` }"
    >
      <div class="loop__title">
        {{ props.sensors.title }}
      </div>
      <div class="loop__note font-mono text-xs">
        {{ props.sensors.note }}
      </div>
    </div>

    <div class="loop__label font-mono text-xs text-center" :style="{ left: `${xs[3]}px`, top: 0, width: `${BOX_W}px` }">
      {{ props.disturbance }}
    </div>
    <div
      class="loop__label loop__label--mid loop__label--feedback font-mono text-xs font-bold text-right"
      :style="{ right: `${W - compareX + 12}px`, top: `${labelY}px` }"
    >
      {{ props.feedback }}
    </div>
    <div
      class="loop__label loop__label--mid font-mono text-xs text-right"
      :style="{ right: `${W - plantX + 12}px`, top: `${labelY}px` }"
    >
      {{ props.output }}
    </div>
  </div>
</template>

<style scoped>
.loop {
  --loop: var(--accent-700);
}

html.dark .loop {
  --loop: var(--accent-300);
}

.loop__node {
  position: absolute;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 0 0.6rem;
  border: 1px solid var(--border);
  border-radius: 10px;
  background: var(--bg-elev);
  text-align: center;
}

.loop__title {
  font-weight: 700;
  line-height: 1.3;
  color: var(--fg);
}

.loop__note {
  margin-top: 0.2rem;
  line-height: 1.35;
  color: var(--fg-muted);
}

.loop__label {
  position: absolute;
  line-height: 1;
  white-space: nowrap;
  color: var(--fg-muted);
}

/* подписи у вертикалей контура стоят по центру зазора между рядами */
.loop__label--mid {
  transform: translateY(-50%);
}

.loop__label--feedback {
  color: var(--loop);
}

.loop__line {
  fill: none;
  stroke: var(--loop);
  stroke-width: 2;
}

.loop__line--feedback {
  stroke-dasharray: 7 6;
  animation: loop-march 1.2s linear infinite;
}

.loop__line-muted {
  fill: none;
  stroke: var(--fg-muted);
  stroke-width: 2;
}

.loop__head {
  fill: var(--loop);
}

.loop__head-muted {
  fill: var(--fg-muted);
}

@keyframes loop-march {
  to {
    stroke-dashoffset: -13;
  }
}

@media (prefers-reduced-motion: reduce) {
  .loop__line--feedback {
    animation: none;
  }
}
</style>
