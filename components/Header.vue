<script setup lang="ts">
defineProps<{
  title?: string;
}>()
</script>

<template>
  <ion-header>
    <ion-toolbar>
        <ion-title>
          {{ title }}
        </ion-title>
      </ion-toolbar>
  </ion-header>
</template>
