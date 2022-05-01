<template>
  <div :id="$style.layout" >

    <SearchBar v-model="searchQuery" :exactMatch="exactMatch"
    @clearQuery="clearQuery()" @addItem="addItem()" @esc="clearQuery()" @enter="addItem()" />

    <div :id="$style.list">
      <div v-for="item in filteredItems" :key="item.id">
        <ListItemComponent :item="item" :exactMatch="exactMatch" @deleteItem="deleteItem(item.id)" />
      </div>
    </div>

    <button @click="alphabeticalSort=true" :class="[$style.sortButton1, alphabeticalSort ? $style.selectedSort: '']">
      Sort by Value</button>

    <button @click="alphabeticalSort=false" :class="[$style.sortButton2, alphabeticalSort ? '': $style.selectedSort]">
      Sort by Added Date</button>
    
  </div>
</template>

<script lang="ts" setup>
import { ref, onMounted, watch } from 'vue'

import SearchBar from "./components/SearchBar.vue";
import ListItemComponent from "./components/ListItem.vue";
  
class ListItem {
  'id': number
  'text': string
  'timeStamp': string

  constructor(id: number, text: string, timeStamp: string) {
    this.id = id
    this.text = text
    this.timeStamp = timeStamp
  }
}

const searchQuery = ref('')
const items = ref([new ListItem(1,'Robert Keřlík','Fri Apr 29 2022 11:09:30 GMT+0200 (Central European Summer Time)')] as ListItem[])
const filteredItems = ref([new ListItem(1,'Robert Keřlík','Fri Apr 29 2022 11:09:30 GMT+0200 (Central European Summer Time)')] as ListItem[])
const exactMatch = ref('')
const numberOfItems = ref(1)
const alphabeticalSort = ref(false)

onMounted(() => {  
  if (localStorage.getItem('savedList')!==null) {
    const retrievedList = localStorage.getItem('savedList') as string
    items.value = JSON.parse(retrievedList)
    filteredItems.value = JSON.parse(retrievedList)
  }
  if (localStorage.getItem('numberOfItems')!==null) {
    const retrievedCount = Number(localStorage.getItem('numberOfItems'))
    numberOfItems.value = retrievedCount
  }
  if (localStorage.getItem('alphabeticalSort')==='yes') {
    alphabeticalSort.value = true
  }

  if (alphabeticalSort.value) {
    filteredItems.value = items.value.sort((a, b) => a.text.localeCompare(b.text))
  }
})

watch(searchQuery, (to, from) => {
  filteredItems.value = []      
  exactMatch.value = ''
  for (let i=0; i<items.value.length; i++) {
    if ( items.value[i].text.toLowerCase().includes(to.toLowerCase()) ) {
      filteredItems.value.push(items.value[i])

      if ( items.value[i].text.toLowerCase() === to.toLowerCase() ) {
        exactMatch.value = items.value[i].text
      }
    }        
  }  
})
watch(alphabeticalSort, (to, from) => {
  if (to===true) {
    filteredItems.value = items.value.sort((a, b) => a.text.localeCompare(b.text))
    localStorage.setItem('alphabeticalSort', 'yes')
  }
  else {
    filteredItems.value = items.value.sort((a, b) => (a.id > b.id) ? 1 : -1)
    localStorage.removeItem('alphabeticalSort') 
  }
})

function clearQuery() {
  searchQuery.value = ''
}
function addItem() {
  console.log(numberOfItems.value)
  const addedItem = new ListItem(numberOfItems.value + 1, searchQuery.value, String(new Date()))
  items.value.push(addedItem)

  filteredItems.value = items.value
  localStorage.setItem('savedList', JSON.stringify(items.value))
  searchQuery.value = ''
  numberOfItems.value = numberOfItems.value + 1
  localStorage.setItem('numberOfItems', String(numberOfItems.value))
  if (alphabeticalSort.value) { filteredItems.value = items.value.sort((a, b) => a.text.localeCompare(b.text)) }
}
function deleteItem(itemId: number) {     
  const itemsAfterDelete = items.value.filter(item => item.id !== itemId)
  items.value = itemsAfterDelete
  localStorage.setItem('savedList', JSON.stringify(items.value))
  filteredItems.value = itemsAfterDelete
}

</script>

<style lang="scss" module>
@use "sass/color";

#layout {
  @include color.layout;
}
#list {
  @include color.list-section;
}
.sortButton1 {
  @include color.sort-button1;   
}
.sortButton2 {
  @include color.sort-button2;
}
.selectedSort {
  @include color.selected-sort;
}
</style>
