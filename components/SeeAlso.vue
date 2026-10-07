<script setup lang="ts">
// Отсылка на свои статьи по теме слайда: подпись и строка ссылок под выводом.
//
// Это не источник: в список на слайде `sources` такие статьи не входят и номера
// через `Cite` не получают. Ссылки пишутся в слоте обычным markdown, поэтому
// открываются в новой вкладке и остаются кликабельными в PDF.
const props = withDefaults(defineProps<{
  label?: string
}>(), {
  label: 'подробнее в моём блоге',
})
</script>

<template>
  <div class="see-also font-mono text-xs">
    <span class="see-also__label font-bold tracking-wider">{{ props.label }}</span>
    <slot />
  </div>
</template>

<style scoped>
.see-also {
  display: flex;
  flex-wrap: wrap;
  align-items: baseline;
  gap: 0.2em 0.9em;
  color: var(--fg-muted);
}

/* markdown в слоте приходит абзацем: отступы темы строке ссылок не нужны */
.see-also :deep(p) {
  margin: 0;
  line-height: 1.6;
}
</style>
