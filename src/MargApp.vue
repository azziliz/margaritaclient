<script setup>
import MenuItems from './components/MenuItems.vue'
</script>

<template>
  <main>
     <div class="wrapper">
      <MenuItems />
    </div>
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

@media (min-width: 1024px) {
  main .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
