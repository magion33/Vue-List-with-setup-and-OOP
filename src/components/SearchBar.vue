<template>      
      
    <input :value="modelValue" @input="$emit('update:modelValue', $event.target.value)" 
    @keydown.esc="$emit('esc')" @keydown.enter="enter()"
    :id="$style.searchBar" type="text" placeholder="Search or Add..." >

    <button v-show="modelValue!==''" @click="clearQuery()" :id="$style.clear" ><Cancel/></button>

    <button v-if="exactMatch===''" v-show="modelValue!==''" @click="$emit('addItem')" :id="$style.add" ><Add/></button>

    <button v-else v-show="modelValue!==''" disabled :id="$style.addDisabled" ><Add/></button>
   
  
</template>

<script lang="ts" setup>
import { defineEmits, defineProps } from 'vue';

import Cancel from "../components/Cancel.vue";
import Add from "../components/Add.vue";

const props = defineProps({
  modelValue: {
    type: String,
    required: true
  },
  exactMatch: {
    type: String,
    required: true
  }
})

const emit = defineEmits(['clearQuery', 'enter'])

function clearQuery() {
  emit('clearQuery')
}
function enter() {
  if (props.exactMatch=='' && props.modelValue!='') {
    emit('enter')
  }
}

</script>

<style lang="scss" module>
@use "sass/color";

#searchBar {
  @include color.search-bar-style;
}
#clear {
  @include color.clear-button;
}
#add {
  @include color.add-button;
}
#addDisabled {
  @include color.add-button-disabled;
}
</style>