<script setup>
import { ref, computed } from 'vue'
import BuilderElement from './BuilderElement.vue'
import JSONPreview from './JSONPreview.vue'

const props = defineProps({
  model: Object,
  schema: Object
})
</script>

<template>
  <BuilderElement :model="props.model" :fields="props.schema"/>
</template>
