<script setup lang="ts">
import { ref } from 'vue';
import Modal from './components/Modal.vue';

const isOpen = ref(false);

const openModal = () => {
  isOpen.value = true;
}

const closeModal = () => {
  isOpen.value = false;
}

</script>

<template>
  <button @click="openModal"> Open modal window </button>
  <Modal v-if="isOpen" @close="closeModal"></Modal>
</template>

