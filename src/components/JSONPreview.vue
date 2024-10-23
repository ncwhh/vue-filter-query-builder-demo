<script setup>
import { computed } from 'vue';
import hljs from 'highlight.js/lib/core';
import json from 'highlight.js/lib/languages/json';

import 'highlight.js/styles/github.css';

hljs.registerLanguage('json', json);

const props = defineProps({
  json: Object
})

const code = computed(() => {
    return hljs.highlight(
      JSON.stringify(props.json, null, 2),
      { language: 'json' }
    ).value
  })
</script>

<template>
  <pre v-html="code" style="padding: 20px;margin: 0;"></pre>
</template>
