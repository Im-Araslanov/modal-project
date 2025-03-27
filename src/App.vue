<script setup>
import HelloWorld from './components/HelloWorld.vue'
import { ref } from 'vue'
import Modal from './components/Modal/Modal.vue'

const showModal = ref(false)
const handleSubmit = ({ reason, orderNumber }) => {
  console.log('Отмена заказа:', orderNumber, 'Причина:', reason);
};
</script>

<template>
  <HelloWorld />
  <div class="app">
    <button @click="showModal = true">Open Modal</button>
    
    <Modal
      :is-open="showModal"
      :on-close="() => showModal = false"
      title="Отказаться от выполнения заказа"
      :order-number="17711"
      @submit="handleSubmit"
    />
  </div>
</template>

<style scoped>
</style>
