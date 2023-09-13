<script setup lang="ts">
import { computed, defineProps } from 'vue';

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
const props = defineProps<{
  module: {
    display: string;
    quantity: number;
    prices: {
      level: number;
      price: number;
      max?: number;
    }[];
  };
}>();

const currentLevel = computed(() => {
  const index = props.module.prices.findIndex((currentPrice) => {
    return currentPrice.level > props.module.quantity;
  });
  return (
    index > -1
      ? props.module.prices[index - 1]
      : props.module.prices[props.module.prices.length - 1]
  ).price;
});

const currentPrice = computed(() => {
  return calModulePrice(props.module);
});

const numberFormat = new Intl.NumberFormat('fr-FR', {
  style: 'currency',
  currency: 'EUR',
});
</script>

<template>
  <div class="flex flex-col gap-1 module">
    <p class="subtitle">{{ module.display }}</p>
    <div class="flex justify-between p-2">
      <input type="number" v-model="module.quantity" />
      <div>
        <p class="text-xl font-semibold">
          {{ numberFormat.format(currentPrice) }}
        </p>
        <p class="subtitle">
          Average :
          <template v-if="module.quantity">{{
            numberFormat.format(currentPrice / module.quantity)
          }}</template>
        </p>
        <p class="subtitle">
          Current level :
          <template v-if="module.quantity">{{ currentLevel }}</template>
        </p>
      </div>
    </div>
  </div>
</template>

<style>
.module {
  width: 70%;
}
</style>
