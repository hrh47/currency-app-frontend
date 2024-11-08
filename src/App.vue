<script setup lang="ts">
import { ref } from 'vue';
import axios, { AxiosError } from 'axios';
import Options from '@/components/Options.vue';
import momentTz from "moment-timezone";

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
const quotedDateText = ref<string | null>(null);

const reset = () => {
  amount.value = 1;
  result.value = null;
  error.value = null;
  quotedDateText.value = null;
};

const convertCurrency = async () => {
  error.value = null;

  if (!amount.value) {
    return;
  }

  try {
    const response = await axios.get(`${import.meta.env.VITE_API_BASE_URL}/exchange-rate`, {
      params: {
        base_currency: baseCurrency.value,
        target_currency: targetCurrency.value
      }
    });

    const exchangeRate = response.data.exchange_rate;
    quotedDateText.value = response.data.quoted_date ? momentTz.tz(response.data.quoted_date, 'Asia/Taipei').format('YYYY/MM/DD hh:mm') : null;
    result.value = (amount.value * exchangeRate).toFixed(5);
  } catch (e) {
    error.value =
      ((e as AxiosError)?.response?.data as { error: string })?.error || (e as Error).message;
  }
};
</script>

<template>
  <div class="sm:container mx-auto m-10">
    <h1 class="text-gray-900 font-bold text-2xl">匯率轉換器</h1>
    <main class="grid grid-cols-7 gap-4 mt-5">
      <div class="col-span-3">
        <Options v-model="baseCurrency" @change="reset" :options="currencies" class="mb-2" />
        <input type="text" v-model="amount"
          class="block w-full rounded-md border-0 py-1.5 pl-7 pr-20 text-gray-900 ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm/6"
          placeholder="0.00" />
      </div>

      <button class="font-bold rounded-md border-0 text-gray-900 ring-1 ring-inset ring-gray-300"
        @click="convertCurrency">
        兌換
      </button>

      <div class="col-span-3">
        <Options v-model="targetCurrency" @change="reset" :options="currencies" class="mb-2" />
        <input type="text" :value="result" readonly
          class="block w-full rounded-md border-0 py-1.5 pl-7 pr-20 text-gray-900 ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm/6"
          placeholder="0.00" />
      </div>
      <section class="col-span-7" v-if="quotedDateText">牌價最新掛牌時間：{{ quotedDateText }}</section>
    </main>
    <section class="text-red-500 mt-2">{{ error }}</section>
  </div>
</template>
