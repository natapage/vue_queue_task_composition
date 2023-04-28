<script setup>
import { ref, onMounted, computed } from "vue";
import checkoutQueue from "./CheckoutQueue.vue";

const queues = ref([[2, 3, 5], [2, 6, 3], [1], [11, 3, 6], []]);
let newPurchasesQuantity = ref("");
let isCorrectQuantity = ref(true);

function addCustomer() {
  newPurchasesQuantity.value = Number(newPurchasesQuantity.value);
  if (
    newPurchasesQuantity.value > 0 &&
    Number.isInteger(newPurchasesQuantity.value)
  ) {
    isCorrectQuantity.value = true;
    queues.value[shortestQueueIndex.value].push(newPurchasesQuantity.value);
    newPurchasesQuantity.value = "";
    return;
  }
  isCorrectQuantity.value = false;
  newPurchasesQuantity.value = "";
}

function updateQueues() {
  queues.value.forEach((queue) => {
    if (queue.length === 0) {
      return;
    }
    if (queue[0] === 1) {
      queue.shift();
      return;
    }
    queue[0] -= 1;
  });
}

const shortestQueueIndex = computed(() => {
  return queues.value
    .map((array, index) => {
      return array.reduce((acc, item) => acc + item, 0);
    })
    .reduce((acc, item, index, arr) => (item < arr[acc] ? index : acc), 0);
});

onMounted(() => setInterval(updateQueues, 2000));
</script>

<template>
  <h2 style="color: red" v-if="!isCorrectQuantity">
    please enter a positive integer
  </h2>
  <div class="input-form">
    <input
      v-model="newPurchasesQuantity"
      type="number"
      class="input"
      placeholder="Add purchases quantity.."
    />
    <button class="btn" @click="addCustomer">Create customer!</button>
  </div>

  <div class="checkout-zone">
    <checkoutQueue
      v-for="(checkout, index) in queues"
      :key="index + 1"
      :checkoutNumber="index + 1"
      :queue="queues[index]"
    ></checkoutQueue>
  </div>
</template>

<style scoped>
.container {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.checkout-zone {
  display: flex;
}
</style>
