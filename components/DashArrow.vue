<script setup lang="ts">
import { useId } from 'vue'

// Пунктирная стрелка во всю ширину слайда: направление влияния между блоками,
// которые стоят над ней. По умолчанию смотрит влево, `to="right"` разворачивает.
//
// Ширина W — контентная область слайда темы: холст 980px минус поля px-16.
const props = withDefaults(defineProps<{
  to?: 'left' | 'right'
}>(), {
  to: 'left',
})

const W = 852
const H = 14

const id = useId()
const arrow = `dash-arrow-${id}`
const d = props.to === 'left' ? `M${W},${H / 2} H2` : `M0,${H / 2} H${W - 2}`
</script>

<template>
  <svg class="dash block mx-auto" :width="W" :height="H" :viewBox="`0 0 ${W} ${H}`" aria-hidden="true">
    <defs>
      <marker :id="arrow" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto">
        <path d="M0,0 L10,5 L0,10 z" class="dash__head" />
      </marker>
    </defs>
    <path :d="d" class="dash__line" :marker-end="`url(#${arrow})`" />
  </svg>
</template>

<style scoped>
.dash {
  --loop: var(--accent-700);
}

html.dark .dash {
  --loop: var(--accent-300);
}

.dash__line {
  fill: none;
  stroke: var(--loop);
  stroke-width: 2;
  stroke-dasharray: 7 6;
  animation: dash-march 1.2s linear infinite;
}

.dash__head {
  fill: var(--loop);
}

@keyframes dash-march {
  to {
    stroke-dashoffset: -13;
  }
}

@media (prefers-reduced-motion: reduce) {
  .dash__line {
    animation: none;
  }
}
</style>
