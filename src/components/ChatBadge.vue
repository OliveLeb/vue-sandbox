<script setup>
import { ref, onMounted } from 'vue'

const obj = ref([
  { id: 1, name: '1', date: 'bla' },
  { id: 2, name: '2' },
  { id: 3, name: '2' },
  { id: 4, name: '2' },
  { id: 5, name: '2' },
  { id: 6, name: '3', date: 'ble' },
  { id: 7, name: '4' },
  { id: 8, name: '4' },
  { id: 9, name: '4' },
  { id: 10, name: '4' },
  { id: 11, name: '4' },
  { id: 12, name: '5', date: 'bli' },
  { id: 13, name: '6' },
  { id: 14, name: '6' },
  { id: 15, name: '6' },
  { id: 16, name: '6' },
  { id: 17, name: '6' },
  { id: 18, name: '7', date: 'blo' },
  { id: 19, name: '6' },
  { id: 20, name: '6' },
  { id: 21, name: '6' },
  { id: 22, name: '6' },
  { id: 23, name: '8' },
])

const badges = ref(null)
</script>

<template>
  <div class="list">
    <div class="item" v-for="item in obj" :key="item.id">
      <template v-if="item.date">
        <div class="date" ref="badges">
          <span>{{ item.date }}</span>
        </div>
      </template>
      <div>
        {{ item.name }}
      </div>
    </div>
  </div>
</template>

<style scoped>
.list {
  border: 1px solid red;
  margin: auto;
  width: 500px;
  height: 800px;
  overflow: auto;
  display: flex;
  flex-direction: column;
  gap: 20px;
}
.item div:not(.date) {
  padding: 15px;
  border: 1px solid black;
  border-radius: 5px;
}
.date {
  text-align: center;
  margin: 10px auto;
}
.date span {
  border-radius: 20%;
  padding: 5px 10px;
  background-color: green;
}
</style>
