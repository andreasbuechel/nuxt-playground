<script setup lang="ts">
const v = ref(false);
</script>

<template>
  <div>
    <h1>{{ v }}</h1>
    <button class="btn-accent" @click="v = !v">Toggle</button>
    <button class="btn-accent" :class="{'btn-disabled': v}">TRADE</button>
  </div>
</template>
