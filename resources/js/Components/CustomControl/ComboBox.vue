<script setup>
import { ref, computed, watch } from 'vue'
import {
  Combobox,
  ComboboxInput,
  ComboboxButton,
  ComboboxOptions,
  ComboboxOption,
  TransitionRoot,
} from '@headlessui/vue'
import { CheckIcon, ChevronUpDownIcon, PlusSmallIcon } from '@heroicons/vue/20/solid'

defineEmits(['update:modelValue']);
const props = defineProps({
    options: {
        type: Array,
        default: () => [],
    },
    modelValue: Object,
    loadOptions: Function,
    color: {
      type: String,
      default: '',
      validator: (value) => ['red', 'orange'].includes(value)
    },
})

const colorClasses = computed(() => {
  return {
    'bg-red-600': props.color === 'red',
    'bg-orange-600': props.color === 'orange'
  };
});

const options = ref(props.options);

const isLoading = ref(false);

let query = ref('')

watch(query, (q) => {
    if(props.loadOptions) {
        isLoading.value = true;
        props.loadOptions(q, (results) => {
            options.value = results;

            //if select an option by mouse click
            //it should bring as selected option
            if(
              props.modelValue && !options.value.some(o => {
                return o.value === props.modelValue?.value;
              })
            ) {
              options.value.unshift(props.modelValue);
            }

            //hide loading text when option comes
            isLoading.value = false;
        })
    }
},{immediate: true})

let filteredOptions = computed(() =>
  query.value === ''
    ? options.value
    : options.value.filter((option) =>
        option.label
          .toLowerCase()
          .replace(/\s+/g, '')
          .includes(query.value.toLowerCase().replace(/\s+/g, ''))
      )
)
</script>

<template>
    <Combobox by="value" :model-value="props.modelValue" @update:model-value="value => $emit('update:modelValue', value)">
      <div class="relative mt-1">
        <div
          class="relative w-full cursor-default overflow-hidden rounded-lg bg-white text-left shadow-md focus:outline-none focus-visible:ring-2 focus-visible:ring-white focus-visible:ring-opacity-75 focus-visible:ring-offset-2 focus-visible:ring-offset-teal-300 sm:text-sm"
        >
          <ComboboxInput
            :class="['w-full border-none py-2 pl-3 pr-10 text-sm leading-5 text-gray-900 focus:ring-0',
              colorClasses
            ]"
            :displayValue="(option) => option.label"
            @change="query = $event.target.value"
          />
          <ComboboxButton
            class="absolute inset-y-0 right-0 flex items-center pr-2"
          >
            <ChevronUpDownIcon
              class="h-5 w-5 text-gray-400"
              aria-hidden="true"
            />
          </ComboboxButton>
        </div>

        <TransitionRoot
          leave="transition ease-in duration-100"
          leaveFrom="opacity-100"
          leaveTo="opacity-0"
          @after-leave="query = ''"
        >
          <ComboboxOptions
            class="absolute mt-1 max-h-60 w-full overflow-auto rounded-md bg-white py-1 text-base shadow-lg ring-1 ring-black ring-opacity-5 focus:outline-none sm:text-sm"
          >
            <div
              v-if="filteredOptions.length === 0 && !isLoading"
              class="relative cursor-default select-none py-2 px-4 text-gray-700"
            >
              Nothing found.
            </div>

            <div
              v-if="isLoading"
              class="relative cursor-default select-none py-2 px-4 text-gray-700"
            >
             Loading...
            </div>

            <template v-if="!isLoading">
                <ComboboxOption
                  v-for="option in filteredOptions"
                  as="template"
                  :key="option.value"
                  :value="option"
                  v-slot="{ selected, active }"
                >
                  <li
                    class="relative cursor-default select-none py-2 pl-10 pr-4"
                    :class="{
                      'bg-teal-600 text-white': active,
                      'text-gray-900': !active,
                    }"
                  >
                    <span
                      class="block truncate"
                      :class="{ 'font-medium': selected, 'font-normal': !selected }"
                    >
                      {{ option.label }}
                    </span>
                    <span
                      v-if="selected"
                      class="absolute inset-y-0 left-0 flex items-center pl-3"
                      :class="{ 'text-white': active, 'text-teal-600': !active }"
                    >
                      <CheckIcon class="h-5 w-5" aria-hidden="true" />
                    </span>
                  </li>
                </ComboboxOption>
            </template>

          </ComboboxOptions>
        </TransitionRoot>
      </div>
    </Combobox>
</template>
