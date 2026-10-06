<script setup lang="ts">
// Ссылка на источник: надстрочный номер, по клику открывается слайд со списком
// источников. Номер совпадает с позицией источника в pages/05-outro/03-sources.md.
const props = defineProps<{
  n: string | number
}>()
</script>

<template>
  <sup class="cite"><SlideLink to="sources" :title="`Источник ${props.n}`">{{ props.n }}</SlideLink></sup>
</template>

<style scoped>
.cite {
  top: -0.7em;
  margin-left: 0.1em;
  font-size: 0.6em;
  font-weight: 700;
}

/* Тема подчёркивает все ссылки пунктиром — у надстрочной цифры он сливается
   со строкой, кликабельность показывает цвет и hover. */
.cite :deep(a) {
  border-bottom: none;
}

.cite :deep(a:hover) {
  border-bottom: 1px solid currentColor;
}
</style>
