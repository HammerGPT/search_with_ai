<script setup lang="ts">
import { RiArrowRightLine, RiCameraLine } from '@remixicon/vue';
import { ref, computed } from 'vue';
import { useI18n } from 'vue-i18n';
import { useAppStore } from '../store';
import { TSearchMode, TSearCategory } from '../interface'; // 确保导入类型

type Emits = {
  (e: 'search', val: string): void,
  (e: 'modeChange', val: TSearchMode): void, // 修正类型
}

type Props = {
  loading: boolean
  autofocus: boolean
  limit?: number
}

const { t } = useI18n();
const appStore = useAppStore();

const props = withDefaults(defineProps<Props>(), {
  limit: 520
});

const isComposing = ref(false);
const showCategoryDropdown = ref(false);
// 删除未使用的变量 showModelDropdown

// Get current mode from store
const currentMode = computed(() => appStore.mode);
const currentCategory = computed(() => appStore.category);
const isLongThinking = ref(false);

const onCompositionStart = () => {
  isComposing.value = true;
};

const onCompositionEnd = () => {
  isComposing.value = false;
};

const emits = defineEmits<Emits>();

const query = defineModel<string>();

const onSearch = () => {
  if (!query.value?.trim()) return;  
  if (isComposing.value) return;
  emits('search', query.value.trim());
};

const onModeChange = (mode: TSearchMode) => { // 确保是正确的类型
  appStore.updateMode(mode);
  emits('modeChange', mode);
};

const onCategoryChange = (category: TSearCategory) => { // 确保是正确的类型
  appStore.updateCategory(category);
  showCategoryDropdown.value = false;
};

const toggleLongThinking = () => {
  isLongThinking.value = !isLongThinking.value;
  // Here you could update a relevant setting in appStore if needed
};

const categories = [
  { name: t('category.general'), value: 'general' as TSearCategory },
  { name: t('category.science'), value: 'science' as TSearCategory },
  { name: t('category.images'), value: 'images' as TSearCategory },
  { name: t('category.videos'), value: 'videos' as TSearCategory }
];

const modes = [
  { name: t('mode.simple'), value: 'simple' as TSearchMode },
  { name: t('mode.deep'), value: 'deep' as TSearchMode },
  { name: t('mode.research'), value: 'research' as TSearchMode }
];
</script>

<template>
  <div class="flex flex-col items-center w-full">
    <!-- Enhanced Search Bar -->
    <div id="searchbar" class="flex w-full flex-row items-center rounded-3xl bg-white p-2 shadow-md transition-all dark:bg-zinc-800 dark:border dark:border-zinc-600">
      <!-- Category Selector -->
      <div class="relative ml-2 mr-2">
        <div @click="showCategoryDropdown = !showCategoryDropdown" class="flex cursor-pointer items-center gap-1 text-sm text-zinc-600 hover:text-blue-500 dark:text-zinc-300">
          <span>{{ t(`category.${currentCategory}`) }}</span>
          <t-icon name="chevron-down" size="12px" />
        </div>
        
        <div v-if="showCategoryDropdown" class="absolute left-0 top-8 z-50 w-32 rounded-lg bg-white p-2 shadow-lg dark:bg-zinc-700">
          <div 
            v-for="category in categories" 
            :key="category.value" 
            @click="onCategoryChange(category.value)"
            class="cursor-pointer rounded px-2 py-1 text-sm hover:bg-gray-100 dark:hover:bg-zinc-600"
            :class="{'bg-gray-100 dark:bg-zinc-600': category.value === currentCategory}"
          >
            {{ category.name }}
          </div>
        </div>
      </div>
      
      <!-- Divider -->
      <div class="mx-1 h-6 w-px bg-gray-300 dark:bg-zinc-600"></div>
      
      <!-- Long Thinking Toggle -->
      <div class="mr-2 flex items-center">
        <label class="inline-flex cursor-pointer items-center">
          <div @click="toggleLongThinking" class="relative h-4 w-8 rounded-full p-1 transition-colors duration-200 ease-in-out" :class="isLongThinking ? 'bg-blue-500' : 'bg-gray-300 dark:bg-zinc-600'">
            <div class="h-3 w-3 transform rounded-full bg-white transition-transform duration-200 ease-in-out" :class="isLongThinking ? 'translate-x-3' : 'translate-x-0'"></div>
          </div>
          <span class="ml-1 text-xs text-gray-600 dark:text-gray-300">长思考·R1</span>
        </label>
      </div>
      
      <!-- Search Input -->
      <div class="grow overflow-hidden rounded-3xl">
        <t-input
          v-model="query"
          :disabled="props.loading"
          :autofocus="autofocus"
          :maxlength="limit"
          :placeholder="t('tips.search')"
          size="large"
          clearable
          @compositionstart="onCompositionStart"
          @compositionend="onCompositionEnd"
          @enter="onSearch"
        >
          <template #suffix>
            <div class="flex items-center gap-2">
              <t-button shape="circle" variant="text" :disabled="loading">
                <template #icon><RiCameraLine size="18" /></template>
              </t-button>
              <t-button :disabled="loading" shape="circle" variant="base" @click="onSearch">
                <template #icon><RiArrowRightLine /></template>
              </t-button>
            </div>
          </template>
        </t-input>
      </div>
    </div>
    
    <!-- Search Modes -->
    <div class="mt-2 flex items-center justify-center">
      <div class="rounded-full bg-gray-100 p-1 dark:bg-zinc-700">
        <t-button 
          v-for="mode in modes" 
          :key="mode.value"
          variant="text"
          shape="round"
          size="small"
          :class="currentMode === mode.value ? 'bg-blue-500 text-white' : 'text-gray-700 dark:text-gray-200'"
          @click="onModeChange(mode.value)"
        >
          {{ mode.name }}
        </t-button>
      </div>
    </div>
    
    <slot></slot>
  </div>
</template>

<style scoped>
#searchbar {
  --td-radius-default: 24px;
}
</style>