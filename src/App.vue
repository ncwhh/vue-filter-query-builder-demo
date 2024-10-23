<script setup>
import { computed, ref } from 'vue'
import Builder from './components/Builder.vue'
import JSONPreview from './components/JSONPreview.vue'
import example from './data/example.json'
import schema from './data/schema.json'

const model = ref(example)

const isValid = computed(() => { return check(model.value)})

function check(m) {
  if (m.combinator !== undefined) {
    return m.rules.some(check)
  } else {
    if (m.field === undefined) return true
    switch (schema[m.field].type) {
      case 'date':
      case 'string':
      case 'number':
        return m.operator === undefined || m.value === undefined
      case 'boolean':
        return m.value === undefined
      case 'enum':
        return m.operator === undefined || m.value === undefined || m.length === 0
      default:
        return false
    }
  }
}
</script>

<template>
  <div class="container">
    <div class="item">
      <span class="header">Filter Query Builder</span>
      <Builder class="scrollable" :model="model"/>
    </div>
    <div class="item">
      <span class="header">Live Output Preview</span>
      <JSONPreview class="scrollable" :json="model" :class="{ invalid_bg: isValid}"/>
    </div>
  </div>
</template>

<style scoped>
.container {
  height: 100vh;
  display: flex;
  flex-direction: row;
  
  @media screen and (max-width: 800px) {
    flex-direction: column;
  }
}
.invalid_bg {
  background-color: rgba(255, 0, 0, 0.2);
}
.item {
  flex: 1;
  display: flex;
  flex-direction: column;
  overflow: auto;
}
.item:not(:last-child) {
  @media screen and (min-width: 801px) {
    border-right: 1px solid gray; 
  }
}
.header {
  padding: 10px;
  background-color: rgb(11, 10, 10);
  color: white;
}
.scrollable {
  flex-grow: 1;
  overflow-y: auto;
}
</style>