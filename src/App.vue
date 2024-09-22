<script setup lang="ts">
import sampleData from './data.json'
import { computed, ref } from 'vue'

let timeout: any
const searchText = ref('')

const data = computed(() => {
  if (searchText.value === '') return sampleData.articles
  const items: typeof sampleData.articles = []
  structuredClone(sampleData.articles).forEach((item) => {
    if (item.title.includes(searchText.value) || item.description.includes(searchText.value)) {
      item.title = item.title.replaceAll(
        searchText.value,
        `<span class="bg-yellow-darken-2">${searchText.value}</span>`
      )
      item.description = item.description.replaceAll(
        searchText.value,
        `<span class="bg-yellow-darken-2">${searchText.value}</span>`
      )
      items.push(item)
    }
  })
  return items
})

const onTextChange = (value: string) => {
  clearTimeout(timeout)
  timeout = setTimeout(() => {
    searchText.value = value
  }, 500)
}
</script>

<template>
  <v-app>
    <v-main>
      <v-app-bar elevation="2">
        <v-app-bar-title>Text Search</v-app-bar-title>
      </v-app-bar>
      <v-container>
        <v-text-field
          variant="outlined"
          density="compact"
          label="Search"
          @update:model-value="onTextChange"
        />
        <div v-if="searchText !== ''">{{ data.length }} articles were found</div>
        <v-list lines="three">
          <template v-for="({ title, description, publishedAt }, index) in data" :key="index">
            <v-list-item>
              <v-list-item-title class="text-h6">
                <template v-slot:default>
                  <div v-html="title" />
                </template>
              </v-list-item-title>
              <v-list-item-subtitle class="font-weight-bold mb-4">
                {{ publishedAt }}
              </v-list-item-subtitle>
              <v-list-item-subtitle>
                <template v-slot:default>
                  <div v-html="description" />
                </template>
              </v-list-item-subtitle>
            </v-list-item>
            <v-divider />
          </template>
        </v-list>
      </v-container>
    </v-main>
  </v-app>
</template>
