<template>
  <div class="site-wrapper">
  <nav></nav>
  <main>
    <RouterView></RouterView>
  </main>
  <footer></footer>
  </div>
</template>

<script setup lang="ts">

</script>

<style scoped>

.site-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 32px;
  width: 100%;

  main {
    flex: 1;
    width: 100%;
  }
}

</style>