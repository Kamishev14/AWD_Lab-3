<script setup>
import { ref, computed } from "vue";
import { useCoinsStore } from "../store/coinsStore";

const coinStore = useCoinsStore();

// null = без сортирање
const sortBy = ref(null);

const sortedCoins = computed(() => {
  const coins = coinStore.coinDataTop50 ?? [];

  // ако нема избрано критериум, прикажи оригинален редослед
  if (!sortBy.value) return coins;

  // НЕ го сортирај директно store array (правиме копија)
  const copy = [...coins];

  if (sortBy.value === "change") {
    return copy.sort(
      (a, b) => Number(b.changePercent24Hr) - Number(a.changePercent24Hr)
    );
  }

  if (sortBy.value === "volume") {
    return copy.sort(
      (a, b) => Number(b.volumeUsd24Hr) - Number(a.volumeUsd24Hr)
    );
  }

  return coins;
});
</script>

<template>
  <div class="bg-[#1B2028] w-full rounded-[10px] p-[20px] mt-[20px] mb-[15px] mx-auto max-w-7xl">
    <h1 class="text-white font-bold text-3xl">Live Market</h1>

    <!-- Buttons (Request 2) -->
    <div class="flex gap-3 mt-[15px]">
      <button
        class="px-4 py-2 rounded-md text-white border border-gray-600 hover:border-white"
        :class="sortBy === 'change' ? 'bg-gray-700' : 'bg-transparent'"
        @click="sortBy = 'change'"
      >
        Sort by Change %
      </button>

      <button
        class="px-4 py-2 rounded-md text-white border border-gray-600 hover:border-white"
        :class="sortBy === 'volume' ? 'bg-gray-700' : 'bg-transparent'"
        @click="sortBy = 'volume'"
      >
        Sort by Volume 24h
      </button>
    </div>

    <div class="grid grid-cols-5 gap-4 mt-[20px] text-gray-400">
      <p>Coin</p>
      <p>Change</p>
      <p>Market Cap</p>
      <p>24h Volume</p>
      <p>Price</p>
    </div>

    <div class="max-h-[400px] overflow-y-scroll">
      <div
        v-for="coin in sortedCoins"
        :key="coin.id"
        class="grid grid-cols-5 gap-4 mt-[20px] text-gray-300"
      >
        <p>{{ coin.name }}</p>

        <p
          :class="Number(coin.changePercent24Hr) < 0
            ? 'font-bold text-red-500'
            : 'font-bold text-[#1ECB4F]'"
        >
          {{ Number(coin.changePercent24Hr).toFixed(2) }}%
        </p>

        <p>${{ Number(coin.marketCapUsd).toFixed(0) }}M</p>
        <p>${{ Number(coin.volumeUsd24Hr).toFixed(0) }}M</p>
        <p>${{ Number(coin.priceUsd).toFixed(4) }}</p>
      </div>
    </div>
  </div>
</template>