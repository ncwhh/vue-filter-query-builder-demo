<script setup>
import { onUpdated } from 'vue'
import schema from '../data/schema.json'

const props = defineProps({
  model: Object,
  // Function provided by the parent component to remove the current rule or group
  remove: Function,
})

// Add new rule object
function addRule() {
  props.model.rules.push({})
}

function updateField() {
  props.model.operator = undefined
  props.model.value = undefined
}

// Remove rule by index
function removeRule(index) {
  props.model.rules.splice(index, 1)
}

// Add new group
function addGroup() {
  props.model.rules.push({
    combinator: 'AND',
    rules: []
  })
}

function getFieldType(field) {
  return field ? schema[field].type : undefined
}
</script>

<template>
  <span>
    <div v-if="model.combinator" class="group" :class="{ invalid: model.rules.length === 0 }">
      <div class="controls">
        <select v-model="model.combinator">
          <option value="AND">AND</option>
          <option value="OR">OR</option>
        </select>
        <button @click="addRule">+ Rule</button>
        <button @click="addGroup">+ Group</button>
        <button v-if="props.remove" class="remove" @click="props.remove">x</button>
      </div>
      <div>
        <Builder
          v-for="(m, i) in model.rules"
          :model="m"
          :remove="() => removeRule(i)"
        />
      </div>
    </div>
    <div v-else class="rule">
      <select
        v-model="model.field"
        :class="{ invalid: model.field === undefined }"
        @change="updateField"
      >
        <option v-for="(meta, id) in schema" :value="id">{{ meta.display_name }}</option>
      </select>
      <template v-if="getFieldType(model.field) === 'date'">
        <select v-model="model.operator" :class="{ invalid: model.operator === undefined }">
          <option value="=">=</option>
          <option value="!=">!=</option>
          <option value=">">&gt;</option>
          <option value="<">&lt;</option>
          <option value=">=">&gt;=</option>
          <option value="<=">&lt;=</option>
        </select>
        <input v-model="model.value" type="date" :class="{ invalid: model.value === undefined }"/>
      </template>
      <template v-else-if="getFieldType(model.field) === 'string'">
        <select v-model="model.operator" :class="{ invalid: model.operator === undefined }">
          <option value="=">=</option>
          <option value="!=">!=</option>
          <option value="contains">contains</option>
          <option value="!contains">!contains</option>
        </select>
        <input v-model="model.value" type="text" :class="{ invalid: model.value === undefined || model.value === '' }"/>
      </template>
      <template v-else-if="getFieldType(model.field) === 'number'">
        <select v-model="model.operator" :class="{ invalid: model.operator === undefined }">
          <option value="=">=</option>
          <option value="!=">!=</option>
          <option value=">">&gt;</option>
          <option value="<">&lt;</option>
          <option value=">=">&gt;=</option>
          <option value="<=">&lt;=</option>
        </select>
        <input v-model="model.value" type="number" :class="{ invalid: model.value === undefined || model.value === '' }"/>
      </template>
      <template v-else-if="getFieldType(model.field) === 'boolean'">
        <select v-model="model.value" :class="{ invalid: model.value === undefined }">
          <option value="true">true</option>
          <option value="false">false</option>
        </select>
      </template>
      <template v-else-if="getFieldType(model.field) === 'enum'">
        <select v-model="model.operator" :class="{ invalid: model.operator === undefined }">
          <option value="in">in</option>
          <option value="not_in">not in</option>
        </select>
        <select v-model="model.value" multiple :class="{ invalid: model.value === undefined || model.value.length === 0 }">
          <option v-for="v in schema[model.field].values" :value="v">{{ v }}</option>
        </select>
      </template>
      <button class="remove" @click="props.remove">x</button>
    </div>
  </span>
</template>

<style>
.group {
  margin: 20px 10px 10px 10px;
  padding: 15px 0 10px 0;
  border: 1px solid gray;
  position: relative;
  border-radius: 5px;
}

.invalid {
  border: 1px solid red;
}

.group>.controls {
  position: absolute;
  top: -12px;
  left: 0;
  display: flex;
  flex-direction: row;
}

.controls>* {
  margin: 0 5px;
}

.rule {
  display: flex;
  flex-direction: row;
  margin: 0 5px;
  padding: 5px;
}

.rule > * {
  margin: 0 5px;
}

.remove {
  background-color: red
}
</style>