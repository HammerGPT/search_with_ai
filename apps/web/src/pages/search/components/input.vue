<script setup lang="ts">
import { ref } from 'vue';
import { useI18n } from 'vue-i18n';
type Emits = {
  (e: 'ask', val: string): void
}

type Props = {
  loading: boolean
  limit?: number
}
const { t } = useI18n();

defineOptions({
  name: 'ChatInput'
});

const props = withDefaults(defineProps<Props>(), {
  limit: 520
});

const isComposing = ref(false);

const emits = defineEmits<Emits>();

const query = ref('');

const onAsk = () => {
  if (isComposing.value) return;
  if (!query.value.trim()) {
    query.value = '';
    return;
  }
  emits('ask', query.value);
  query.value = '';
};

const onCompositionStart = () => {
  isComposing.value = true;
};

const onCompositionEnd = () => {
  isComposing.value = false;
};
</script>

<template>
  <div id="ask" class="flex items-center justify-center">
    <div class="w-10/12 overflow-hidden rounded-3xl border border-gray-100 bg-white dark:border-gray-300 dark:bg-zinc-700">
      <t-input
        v-model="query"
        :disabled="props.loading"
        clearable
        :autofocus="false"
        :maxlength="limit"
        size="large"
        :placeholder="t('tips.continue')"
        @compositionstart="onCompositionStart"
        @compositionend="onCompositionEnd"
        @enter="onAsk"
      />
    </div>
  </div>
</template>


<style scoped>
#ask {
  --td-radius-default: 24px;
}
</style>
