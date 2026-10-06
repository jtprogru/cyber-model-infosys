<script setup lang="ts">
import { computed, useId } from 'vue'

// Петля положительной обратной связи: узлы в ряд, сплошные стрелки слева
// направо и пунктирный возврат от последнего узла петли к первому с подписью.
//
// `lead` — узлы до петли: то, что привело систему в неё. Они стоят в том же
// ряду, но нейтральные, а возврат охватывает только узлы `nodes`.
//
// Геометрия считается от числа узлов и констант ниже, без замеров DOM.
// Ширина W — контентная область слайда темы: холст 980px минус поля px-16.
interface Node { title: string, note: string }

const props = withDefaults(defineProps<{
  lead?: Node[]
  nodes: Node[]
  label: string
}>(), {
  lead: () => [],
})

const W = 852
const GAP = 24
const BOX_H = 84
const DROP = 30
const H = BOX_H + DROP + 12

const all = computed(() => [
  ...props.lead.map(node => ({ ...node, inLoop: false })),
  ...props.nodes.map(node => ({ ...node, inLoop: true })),
])
const boxW = computed(() => (W - GAP * (all.value.length - 1)) / all.value.length)
const xs = computed(() => all.value.map((_, i) => i * (boxW.value + GAP)))
const firstX = computed(() => xs.value[props.lead.length] + boxW.value / 2)
const lastX = computed(() => W - boxW.value / 2)
// подпись стоит посередине линии возврата, а не посередине всей схемы
const labelX = computed(() => (firstX.value + lastX.value) / 2)

const id = useId()
const loopArrow = `chain-loop-${id}`
const leadArrow = `chain-lead-${id}`
</script>

<template>
  <div class="chain relative mx-auto" :style="{ width: `${W}px`, height: `${H}px` }">
    <svg class="absolute inset-0" :width="W" :height="H" :viewBox="`0 0 ${W} ${H}`" aria-hidden="true">
      <defs>
        <marker :id="loopArrow" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto">
          <path d="M0,0 L10,5 L0,10 z" class="chain__head" />
        </marker>
        <marker :id="leadArrow" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto">
          <path d="M0,0 L10,5 L0,10 z" class="chain__head-lead" />
        </marker>
      </defs>
      <!-- стрелка, которая входит в петлю или идёт до неё, нейтральная -->
      <path
        v-for="(x, i) in xs.slice(0, -1)"
        :key="x"
        :d="`M${x + boxW + 2},${BOX_H / 2} H${x + boxW + GAP - 2}`"
        :class="all[i].inLoop ? 'chain__line' : 'chain__line-lead'"
        :marker-end="`url(#${all[i].inLoop ? loopArrow : leadArrow})`"
      />
      <path
        :d="`M${lastX},${BOX_H + 2} V${BOX_H + DROP} H${firstX} V${BOX_H + 2}`"
        class="chain__line chain__line--back"
        :marker-end="`url(#${loopArrow})`"
      />
    </svg>

    <div
      v-for="(node, i) in all"
      :key="node.title"
      class="chain__node"
      :class="{ 'chain__node--loop': node.inLoop }"
      :style="{ left: `${xs[i]}px`, width: `${boxW}px`, height: `${BOX_H}px` }"
    >
      <div class="chain__title">
        {{ node.title }}
      </div>
      <div class="chain__note">
        {{ node.note }}
      </div>
    </div>

    <div class="chain__label font-mono text-xs font-bold" :style="{ left: `${labelX}px`, top: `${BOX_H + DROP}px` }">
      {{ props.label }}
    </div>
  </div>
</template>

<style scoped>
.chain {
  --loop: var(--accent-700);
}

html.dark .chain {
  --loop: var(--accent-300);
}

.chain__node {
  position: absolute;
  top: 0;
  display: flex;
  flex-direction: column;
  justify-content: center;
  padding: 0 0.75rem;
  border: 1px solid var(--border);
  border-radius: 10px;
  background: var(--bg-elev);
}

.chain__node--loop {
  border-color: var(--accent-400);
  background: color-mix(in srgb, var(--accent-400) 7%, var(--bg-elev));
}

.chain__title {
  font-size: 1.15em;
  font-weight: 700;
  line-height: 1.3;
  color: var(--fg);
}

.chain__node--loop .chain__title {
  color: var(--loop);
}

.chain__note {
  margin-top: 0.15rem;
  font-size: 0.85em;
  line-height: 1.35;
  color: var(--fg-muted);
}

/* подпись лежит на линии возврата и закрывает её фоном слайда */
.chain__label {
  position: absolute;
  transform: translate(-50%, -50%);
  padding: 0 0.75rem;
  line-height: 1;
  white-space: nowrap;
  background: var(--bg);
  color: var(--loop);
}

.chain__line {
  fill: none;
  stroke: var(--loop);
  stroke-width: 2;
}

.chain__line--back {
  stroke-dasharray: 7 6;
  animation: chain-march 1.2s linear infinite;
}

.chain__line-lead {
  fill: none;
  stroke: var(--fg-muted);
  stroke-width: 2;
}

.chain__head {
  fill: var(--loop);
}

.chain__head-lead {
  fill: var(--fg-muted);
}

@keyframes chain-march {
  to {
    stroke-dashoffset: -13;
  }
}

@media (prefers-reduced-motion: reduce) {
  .chain__line--back {
    animation: none;
  }
}
</style>
