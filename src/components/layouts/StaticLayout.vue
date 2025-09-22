<template>
  <div class="content">
    <h1>{{title}}</h1>
    <slot></slot>
  </div>
</template>

<script setup lang="ts">

const {title} = defineProps<{
  title: string
}>()

</script>

<style scoped>

.content {
  width: calc(100% - 54px);
  flex-direction: column;
  gap: 32px;
  margin: 0 auto;
}

@media (min-width: 740px) {
  .content {
    width: 640px;
  }
}

@media (min-width: 1200px) {
  .content {
    width: 900px;
  }
}

@media (min-width: 1920px) {
  .content {
    width: 1400px;
  }
}

</style>