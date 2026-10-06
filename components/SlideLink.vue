<script setup lang="ts">
import { useNav } from '@slidev/client'
import { getSlide } from '@slidev/client/logic/slides.ts'
import { computed } from 'vue'

// Ссылка на слайд по routeAlias, которая работает и в PDF.
//
// Встроенный Link в режиме печати ставит href="#<to>", а слайды на печатной
// странице получают id вида 019-01: номер слайда и шаг клика. Ссылка по алиасу
// в PDF поэтому никуда не ведёт. Здесь алиас переводится в номер слайда.
const props = defineProps<{
  to: string | number
}>()

const { isPrintMode } = useNav()

const anchor = computed(() => {
  const no = getSlide(props.to)?.no
  return no ? `#${String(no).padStart(3, '0')}-01` : `#${props.to}`
})
</script>

<template>
  <a v-if="isPrintMode" :href="anchor"><slot /></a>
  <RouterLink v-else :to="String(props.to)" @click="($event.target as HTMLElement)?.blur()">
    <slot />
  </RouterLink>
</template>
