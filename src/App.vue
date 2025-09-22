<template>
  <div class="site-wrapper">
  <nav></nav>
  <main>
    <RouterView></RouterView>
  </main>
  <footer></footer>
    <button @click="scrollToTop" class="scroll-btn">
      <img :src="getImage('ic_chevron_top.png')" alt="top scroll">
    </button>
  </div>
</template>

<script setup lang="ts">

import {getImage} from "@/helpers/getImage.ts";

function scrollToTop() {
  window.scrollTo({top: 0, behavior: "smooth"})
}
</script>

<style scoped>

.site-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 32px;
  width: 100%;
  height: 100dvh;
  position: relative;

  main {
    flex: 1;
    width: 100%;
  }

  .scroll-btn {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 80px;
    height: 80px;
    border-radius: 50%;
    position: fixed;
    bottom: 200px;
    right: 100px;
    background: white;
    border: 2px solid black;
    cursor: pointer;
    transition: all 200ms ease-in-out;

    &:hover {
      background: lightgrey;
    }
  }
}

</style>