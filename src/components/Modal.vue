<script setup>
import { ref } from 'vue'
  import BaseInput from './BaseInput.vue';
  const valInput = ref("")

  defineProps({
    ok: {
      type: Object,
      default: null
    },
    cancel: {
      type: Object,
      default: null
    }
  })

</script>


<template>
    <div class="container">
      <div class="modal">
        <slot name="title" title="Le titre"/>

        <BaseInput v-model="valInput" />

        <slot name="list" :lol="valInput"/>

        <div>
          <button @click="ok.action">{{ok.title}}</button>
          <button @click="cancel.action">{{cancel.title}}</button>
        </div>
      </div>
    </div>
</template>

<style>
.container {
  position:fixed;
  inset: 0;
  background: rgba(0,0,0,0.5)
}
.modal {
  position: absolute;
  top: 15rem;
  left: 50%;
  transform: translateX(-50%);
  width: 30rem;
  height: 30rem;
  border-radius: 5%;
  background-color: #fff;
  padding: 2rem;
}
</style>