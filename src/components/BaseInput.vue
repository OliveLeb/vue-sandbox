<script setup>
  defineProps({
    modelValue: {
      type: String,
      default: ''
    }
  })

  defineEmits(['send'])
</script>


<template>
  <input type="text" :value="modelValue" @input="$emit('update:modelValue', $event.target.value)" />
</template>