<script setup>
  import Modal from "./Modal.vue"
  import {ref} from "vue"

  defineProps({
    show: {
      type: Boolean,
      default: false
    }
  })
  const emit = defineEmits(['close-modal', 'resolve-promise'])

  const selected = ref(null)

  const list = [1,2,3,4,5,6,7,8,9]

  const ok = {
    title: 'Ok',
    action:() => {
      console.log(selected.value)
      emit('resolve-promise', "transferModal", selected.value)
    }
  }
  const cancel = {
    title: 'Cancel',
    action:() => emit('close-modal')
  }


  const resolve = (state) => {
    state.resolve(selected.value)
    state = null
  }

</script>


<template>
  <Modal v-if="show" :ok="ok" :cancel="cancel" >
    <template #title="{title}">
      <h1>{{title}}</h1>
    </template>

    <template #list="{lol}" >
      <div>List</div>
      <p v-for="item in list" :key="item">
      <input type="radio" :value="item" v-model="selected">
      <label>{{item}}</label>
      </p>
    </template>
  </Modal>
</template>