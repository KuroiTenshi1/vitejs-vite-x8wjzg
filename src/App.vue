<script setup lang="ts">
import module from './components/module.vue';
import modulesJson from './components/module.json'

const calModulePrice = (module) => {
  return module.prices.reduce((acc, price) => {
    if (module.quantity < price.level) return acc;
    if (
      module.quantity >= price.level &&
      (!price.max || module.quantity < price.max)
    ) {
      return acc + (module.quantity - price.level + 1) * price.price;
    }
    return acc + (price.max - price.level + 1) * price.price;
  }, 0);
};

import { ref, computed } from 'vue';
const modules = ref(modulesJson);

const total = computed(() => {
  let total = 0;
  for (const key in modules.value) {
    total += calModulePrice(modules.value[key]);
  }
  return total;
});

const numberFormat = new Intl.NumberFormat('fr-FR', {
  style: 'currency',
  currency: 'EUR',
});
</script>

<template>
  <div class="page gap-5">
    <h1 class="page-title">Pricing calculator</h1>
    <div class="flex flex-col card-base gap-5 module-container">
      <module v-for="module in modules" :module="module" />
    </div>
    <div class="flex justify-between card-base">
      <p class="text-xl font-semibold">Total :</p>
      <p class="text-3xl font-bold text-success-400">
        {{ numberFormat.format(total) }}
      </p>
    </div>
  </div>
</template>

<style>
.page {
  display: flex;
  flex-direction: column;
  padding: 10px;
  width: 100%;
}

.flex-col {
  flex-direction: column;
}

.justify-between {
  justify-content: space-between;
}

.flex {
  display: flex;
}

.card-base {
  background: #29292c;
  padding: 5px;
}

.gap-5 {
  gap: 20px;
}

.module-container {
  width: 100%;
}
</style>
