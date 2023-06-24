<script setup lang="ts">

defineProps<{ msg: string }>();

</script>

<template>
  <div>
    <h1 class="text-[#43d9ad] font-extrabold " >{{ msg }}</h1>
  </div>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}
</style>
