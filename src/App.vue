<template>
  <div class="wrapper">
    <TheHeader />
    <main class="main"><AppMain /></main>
    <TheFooter />
  </div>
</template>

<script setup>
import TheHeader from './components/TheHeader.vue';
import AppMain from './components/AppMain.vue';
import TheFooter from './components/TheFooter.vue';
</script>

<style>

.wrapper {
  width: 100%;
  max-width: 1200px; /* 最大幅を設定 */
  height: 98vh;
  margin: 0 auto; /* 横方向の中央揃え */
  padding: 0;
  display: flex;
  flex-direction: column;
  justify-content: center; /* 縦方向の中央揃え */
}

.main {
  flex: 1;
  padding: 1em;
  display: flex;
  flex-direction: column;
  justify-content: start;
}

@media (max-width: 1200px) {
  .wrapper {
    width: 100%; /* ビューポート幅に応じて幅を調整 */
  }
}

</style>
