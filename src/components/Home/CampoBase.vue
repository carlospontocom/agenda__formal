<template>
  <input
    :type="type"
    class="field"
    :placeholder="placeholder"
    v-model="model"
  />
</template>

<script setup lang="ts">
const props = defineProps<{
  placeholder: string;
  type: 'text' | 'email' | 'tel';
}>();

const model = defineModel<string>();
</script>

<style>
.field {
  padding: 12px;
  width: 100%;
}
</style>
