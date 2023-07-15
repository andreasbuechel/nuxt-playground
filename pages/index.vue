<script setup lang="ts">
import { ref, computed } from "vue";

type Pair = string;
type Direction = "BUY" | "SELL";
type Price = string;

const pairs = [
  { id: "EURUSD", rate: 1.307 },
  { id: "USDJPY", rate: 181 },
  { id: "GBPUSD", rate: 1.307 },
  { id: "AUDUSD", rate: 0.6861 },
  { id: "USDCAD", rate: 1.316 },
  { id: "USDCHF", rate: 0.8625 },
  { id: "NZDUSD", rate: 0.6361 },
  { id: "EURJPY", rate: 154.8 },
  { id: "GBPJPY", rate: 138.5 },
];

const pairRates = Object.fromEntries(pairs.map(({ id, rate }) => [id, rate]));

function randomPrice(pair: Pair, dir: Direction) {
  let val = pairRates[pair] * (1 + Math.random() * 0.0001);
  val = dir === "BUY" ? val : 1 / val;
  return val.toPrecision(6);
}

const pair = ref<string | null>(null);
const direction = ref<Direction>("BUY");
const tickedPrice = ref<string | null>(null);
const trading = ref(false);
const tradedPrice = ref<Price | null>(null);

const ticking = computed(
  () => pair.value && !trading.value && tradedPrice.value === null
);
const traded = computed(() => tradedPrice.value !== null);
const readyToTrade = computed(
  () => tickedPrice.value !== null && !trading.value && !traded.value
);

setInterval(() => {
  if (ticking.value) {
    tickedPrice.value = randomPrice(pair.value!, direction.value);
  }
}, 500);

watch([pair, direction], () => {
  tickedPrice.value = null;
  tradedPrice.value = null;
});

watch(trading, (newValue) => {
  if (newValue) {
    setTimeout(() => {
      tradedPrice.value = randomPrice(pair.value!, direction.value);
      trading.value = false;
    }, 1000);
  }
});
</script>

<template>
  <div>
    <select class="select" v-model="pair" :disabled="trading">
      <option disabled selected>Pick a pair</option>
      <option v-for="pair in pairs" :value="pair.id">
        {{ pair.id }}
      </option>
    </select>
    <input type="radio" className="radio" v-model="direction" value="BUY" />
    <span>BUY</span>
    <input type="radio" className="radio" v-model="direction" value="SELL" />
    <span>SELL</span>
    <div>
      <!-- v-if="pair" -->
      <button
        v-if="pair"
        class="btn-accent"
        :class="{ 'btn-disabled': !readyToTrade }"
        @click="trading = true"
      >
        {{ tickedPrice || "Fetching..." }}
      </button>
    </div>
    <div v-if="trading">Trading...</div>
    <div v-if="tradedPrice !== null">Traded at {{ tradedPrice }}</div>
  </div>
</template>
