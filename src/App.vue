<script>

const handleModal = async (open, modal) => {
      const zebi = await open(modal)
      if (!zebi) return console.log("non")
      console.log(zebi)
      modal.show = false
}

import Transfer from "@/components/Transfer.vue"
import ComputedList from "@/components/ComputedList.vue"
export default({
  components: {Transfer,ComputedList},
  data() {
    return {
      transferModal: {
        show: false,
        state: null
      },
      call: {
        type: "FREECALL",
        participants: [1]
      }
    }
  },
  methods: {
    open(modal) {
      modal.show = true
      return new Promise(resolve => modal.state = {resolve})
    },
    handleClick(modal) {
      handleModal(this.open, modal)
    },
    resolvePromise(modal, res) {
      this[modal].state.resolve(res)
      this[modal].state = null
    }
  }
})

</script>

<template>
<div>
  <button @click="handleClick(transferModal)">open</button>
  <ComputedList :call="call"/>
</div>
  <Teleport to="body">
    <Transfer :show="transferModal.show" @close-modal="transferModal.show = false" @resolve-promise="resolvePromise"/>
  </Teleport>
</template>

<style>
* {
  margin:0;
  padding:0;
  list-style-type: none;
  box-sizing: border-box;
}
</style>
