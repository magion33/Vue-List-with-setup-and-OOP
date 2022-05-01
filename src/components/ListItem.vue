<template>      
      
    <div :class="$style.item" @mouseover="showTrash=true" @mouseout="showTrash=false">

      <div v-if="exactMatch!==item.text" style="float:left">
        <p>{{item.text}}</p>
        <p>#{{item.id}}</p>
      </div>

      <div v-else style="float:left;">
        <div style="float:left;"><Check :id="$style.check" /></div>
        <div style="float:left;">
          <p>{{item.text}}</p>
          <p><span :id="$style.exactMatch" >Exact match&nbsp;</span>#{{item.id}}</p>  
        </div>        
      </div>

      <div style="float:right; line-height:65px; margin-right:20px;"><Trash v-show="showTrash" @click="$emit('deleteItem')" :id="$style.trash" /></div>
      <div style="float:right; line-height:65px; margin-right:20px;">{{formated}}</div>

    </div>
  
</template>

<script lang="ts" setup>
import { ref, onMounted, defineProps } from 'vue'

import Check from "../components/Check.vue";
import Trash from "../components/Trash.vue";

import { format, formatDistance, formatRelative, subDays } from 'date-fns'

const props = defineProps({
  item: {
    type: Object,
    required: true
  },
  exactMatch: {
    type: String,
    required: true
  }
})

const showTrash = ref(false)
const formated = ref('')

onMounted(() => {
  // real time update
  formated.value = formatDistance(new Date(props.item.timeStamp), new Date(), { addSuffix: true })    
  setInterval( () => {
    formated.value = formatDistance(new Date(props.item.timeStamp), new Date(), { addSuffix: true })
  }, 1000)
})

</script>

<style lang="scss" module>
@use "sass/color";

.item {
  @include color.list-item;
}
#check {
  @include color.check;
}
#exactMatch {
  @include color.exact-match;
}
#trash {
  @include color.trash;
}
</style>
