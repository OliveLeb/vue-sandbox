<script>
const handleModal = async (open, modal) => {
  const zebi = await open(modal)
  if (!zebi) return console.log('non')
  console.log(zebi)
  modal.show = false
}

import Transfer from '@/components/Transfer.vue'
import ComputedList from '@/components/ComputedList.vue'
import ChatBadge from '@/components/ChatBadge.vue'
import Recorder from '@/components/Recorder.vue'
export default {
  components: { Transfer, ComputedList, ChatBadge, Recorder },
  data() {
    return {
      transferModal: {
        show: false,
        state: null,
      },
      call: {
        type: 'FREECALL',
        participants: [1],
      },
    }
  },
  methods: {
    open(modal) {
      modal.show = true
      return new Promise((resolve) => (modal.state = { resolve }))
    },
    handleClick(modal) {
      handleModal(this.open, modal)
    },
    resolvePromise(modal, res) {
      this[modal].state.resolve(res)
      this[modal].state = null
    },
  },
}
</script>

<template>
  <div>
    <button @click="handleClick(transferModal)">open</button>
    <select v-model="call.type">
      <option value="FREECALL">FREECALL</option>
      <option value="EMERGENCY">Emergency</option>
    </select>
    <ComputedList :call="call" />
  </div>
  <Recorder />
  <!-- <ChatBadge /> -->
  <Teleport to="body">
    <Transfer
      :show="transferModal.show"
      @close-modal="transferModal.show = false"
      @resolve-promise="resolvePromise"
    />
  </Teleport>
</template>

<style>
* {
  margin: 0;
  padding: 0;
  list-style-type: none;
  box-sizing: border-box;
}
</style>
