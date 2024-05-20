<script setup>
import RoomCountBox from './components/RoomCountBox.vue';
import spinalcore from './components/spinalcore.vue'
</script>

<template>
  <main>
    <!-- <img src="../public/spinalcom_anim_home-2 (1).gif" > -->
    
    <spinalcore />
    
  </main>
</template>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

img {
  
  background-repeat: no-repeat;
  background-position: center center;
  background-size: cover; 
}
@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }

}
</style>
