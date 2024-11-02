<script setup lang="ts">
import {
  Listbox,
  ListboxButton,
  ListboxOptions,
  ListboxOption,
} from '@headlessui/vue'
import { CheckIcon, ChevronUpDownIcon } from '@heroicons/vue/20/solid'
import { ref } from 'vue';

const props = defineProps<{
  options: { code: string, text: string }[],
  modelValue: string
}>()
const emits = defineEmits<{
  'update:modelValue': [modelValue: string]
  change: []
}>()
const currentOption = ref<{ code: string, text: string } | undefined>(props.options.find(option => option.code === props.modelValue));
</script>

<template>
  <Listbox :modelValue="modelValue" @update:modelValue="($event) => {
    currentOption = $event;
    emits('update:modelValue', $event.code)
    emits('change')
  }" class="mb-2">
    <div class="relative mt-1">
      <ListboxButton
        class="block w-full rounded-md border-0 py-1.5 pl-7 pr-20 text-gray-900 ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm/6">
        <span class="block truncate" v-if="currentOption">{{ currentOption.text }} ({{ currentOption.code
          }})</span>
        <span class="pointer-events-none absolute inset-y-0 right-0 flex items-center pr-2">
          <ChevronUpDownIcon class="h-5 w-5 text-gray-400" aria-hidden="true" />
        </span>
      </ListboxButton>

      <transition leave-active-class="transition duration-100 ease-in" leave-from-class="opacity-100"
        leave-to-class="opacity-0">
        <ListboxOptions
          class="absolute mt-1 max-h-60 w-full overflow-auto rounded-md bg-white py-1 text-base shadow-lg ring-1 ring-black/5 focus:outline-none sm:text-sm">
          <ListboxOption v-slot="{ active, selected }" v-for="(option, i) in options" :key="i" :value="option"
            as="template">
            <li :class="[
              active ? 'bg-amber-100 text-amber-900' : 'text-gray-900',
              'relative cursor-default select-none py-2 pl-10 pr-4',
            ]">
              <span :class="[
                selected ? 'font-medium' : 'font-normal',
                'block truncate',
              ]">{{ option.text }} ({{ option.code }})</span>
              <span v-if="selected" class="absolute inset-y-0 left-0 flex items-center pl-3 text-amber-600">
                <CheckIcon class="h-5 w-5" aria-hidden="true" />
              </span>
            </li>
          </ListboxOption>
        </ListboxOptions>
      </transition>
    </div>
  </Listbox>
</template>

<style scoped></style>
