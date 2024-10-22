<template>
  <header>
    <p>Hello World Header</p>
  </header>

  <main>
    <Adventurer :size="100" :hair="{ variant: 'hair-17', color: 'purple' }" />
  </main>
</template>

<script setup lang="ts">
import { Adventurer } from 'vue-adventurer'
</script>
