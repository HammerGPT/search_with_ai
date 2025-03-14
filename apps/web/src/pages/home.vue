<template>
  <div id="home" class="flex size-full items-center justify-center dark:bg-black">
    <div class="mt-36 flex w-full flex-col gap-4 p-4 sm:-mt-28 lg:max-w-3xl xl:max-w-4xl">
      <div class="flex items-center justify-center gap-2 mb-6">
        <img :src="logoUrl" class="w-10" />
        <span class="text-3xl font-bold dark:text-gray-100">AI Search</span>
        <t-tag variant="light" class="text-xs text-gray-500">beta</t-tag>
      </div>
      
      <!-- Enhanced Search Input Bar -->
      <SearchInputBar 
        :autofocus="true" 
        :loading="false" 
        @search="search" 
        @mode-change="onModeChange"
      />
      
      <div class="flex w-full justify-center mt-6">
        <div class="flex flex-wrap justify-center gap-2">
          <t-tag
            v-for="(item, index) in list"
            :key="index" shape="round" 
            variant="outline"
            size="medium"
            class="cursor-pointer hover:opacity-80"
            @click="onQuickSearch(item)"
          >
            {{ item }} <RiSearch2Line class="ml-1" size="12"/>
          </t-tag>
        </div>
      </div>
      
      <div class="mt-10">
        <PageFooter @click="onOpenSettings" />
      </div>
    </div>
    
    <!-- Settings Drawer -->
    <AppSettings v-model="showSettings" />
  </div>
</template>

<script setup lang="tsx">
import router from '@/router';
import { RiSearch2Line } from '@remixicon/vue';
import { PageFooter, SearchInputBar, AppSettings } from '@/components';
import logoUrl from '@/assets/logo.png';
import { useI18n } from 'vue-i18n';
import { computed, ref } from 'vue';
import { useAppStore } from '@/store';
import { TSearchMode } from '@/interface'; // 导入必要的类型

const { locale } = useI18n();
const appStore = useAppStore();
const showSettings = ref(false);

const quickly: Record<string, string[]> = {
  zh: [
    '怎么使用Ollama在本地部署大模型?',
    '70b模型一般需要什么样的硬件配置？',
    'DeepSeek R1和o3-mini有什么区别？',
  ],
  en: [
    'What is LLM?',
    'What is RAG?',
    'How to use LLM in enterprise?'
  ],
  ptBR: [
    "O que é LLM?",
    "O que é RAG?",
    "Como utilizar LLM em empresas?"
  ]
};

const list = computed(() => {
  const key = locale.value;
  return quickly[key];
});

const search = (val: string) => {
  if (!val) {
    return;
  }
  if (appStore.mode === 'research') {
    router.push({
      name: 'DeepResearch',
      params: {
        query: val
      }
    });
  } else {
    router.push({
      name: 'SearchPage',
      query: {
        q: val
      }
    });
  }
};

const onModeChange = (mode: TSearchMode) => { // 确保使用正确的类型
  // Mode is already updated in store by the SearchInputBar component
  // This handler is for any additional actions needed at the page level
  console.log(`Search mode changed to: ${mode}`);
};

const onQuickSearch = (val: string) => {
  search(val);
};

const onOpenSettings = () => {
  showSettings.value = true;
};
</script>

<script lang="tsx">
export default {
  name: 'HomePage'
};
</script>