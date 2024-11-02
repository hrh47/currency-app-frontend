<script setup lang="ts">
import { ref } from 'vue';
import axios, { AxiosError } from 'axios';

const currencies = ref([
  { code: 'TWD', text: '新臺幣' },
  { code: 'USD', text: '美金' },
  { code: 'HKD', text: '港幣' },
  { code: 'GBP', text: '英鎊' },
  { code: 'AUD', text: '澳幣' },
  { code: 'CAD', text: '加拿大幣' },
  { code: 'SGD', text: '新加坡幣' },
  { code: 'CHF', text: '瑞士法郎' },
  { code: 'JPY', text: '日圓' },
  { code: 'ZAR', text: '南非幣' },
  { code: 'SEK', text: '瑞典幣' },
  { code: 'NZD', text: '紐元' },
  { code: 'THB', text: '泰幣' },
  { code: 'PHP', text: '菲國比索' },
  { code: 'IDR', text: '印尼幣' },
  { code: 'EUR', text: '歐元' },
  { code: 'KRW', text: '韓元' },
  { code: 'VND', text: '越南盾' },
  { code: 'MYR', text: '馬來幣' },
  { code: 'CNY', text: '人民幣' }
]);
const baseCurrency = ref('TWD');
const targetCurrency = ref('USD');
const amount = ref(1);
const result = ref<string | null>(null);
const error = ref<string | null>(null);

const reset = () => {
  amount.value = 1;
  result.value = null;
  error.value = null;
};

const convertCurrency = async () => {
  if (!amount.value) {
    return;
  }

  try {
    const response = await axios.get('http://localhost:3000/exchange-rate', {
      params: {
        base_currency: baseCurrency.value,
        target_currency: targetCurrency.value
      }
    });

    const exchangeRate = response.data.exchange_rate;
    result.value = (amount.value * exchangeRate).toFixed(5);
  } catch (e) {
    error.value =
      ((e as AxiosError)?.response?.data as { error: string })?.error || (e as Error).message;
  }
};
</script>

<template>
  <div>
    <h1>匯率轉換器</h1>
    <main>
      <div>
        <select v-model="baseCurrency" @change="reset">
          <option v-for="currency in currencies" :key="currency.code" :value="currency.code">
            {{ currency.text }} ({{ currency.code }})
          </option>
        </select>
        <input type="text" v-model="amount" placeholder="0.00" />
      </div>

      <button @click="convertCurrency">
        兌換
      </button>

      <div class="col-span-3">
        <select v-model="targetCurrency" @change="reset">
          <option v-for="currency in currencies" :key="currency.code" :value="currency.code">
            {{ currency.text }} ({{ currency.code }})
          </option>
        </select>
        <input type="text" :value="result" readonly placeholder="0.00" />
      </div>
    </main>
    <section :style="{ 'color': 'red' }">{{ error }}</section>
  </div>
</template>
