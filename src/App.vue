<script setup>
import { ref } from 'vue'
import JsonEditor from './components/JsonEditor.vue'
import JsonDiff from './components/JsonDiff.vue'

const currentTab = ref('editor')
const tabs = [
  { id: 'editor', name: 'JSON 编辑器' },
  { id: 'diff', name: 'JSON 对比' }
]
</script>

<template>
  <div class="app">
    <main>
      <div class="tabs">
        <button 
          v-for="tab in tabs" 
          :key="tab.id"
          :class="{ active: currentTab === tab.id }"
          @click="currentTab = tab.id"
        >
          {{ tab.name }}
        </button>
      </div>
      <div class="content">
        <JsonEditor v-if="currentTab === 'editor'" />
        <JsonDiff v-if="currentTab === 'diff'" />
      </div>
    </main>
  </div>
</template>

<style>
/* 覆盖 main.css 中的媒体查询 */
body {
  display: block !important;
  place-items: initial !important;
}

#app {
  display: flex !important;
  grid-template-columns: initial !important;
  padding: 0 !important;
}

.app {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  background-color: #f5f5f5;
  padding: 1rem;
  width: 100%;
}

main {
  flex: 1;
  width: 100%;
  display: flex;
  flex-direction: column;
}

.tabs {
  display: flex;
  gap: 1rem;
  margin-bottom: 1rem;
}

.tabs button {
  padding: 0.5rem 1rem;
  border: none;
  border-radius: 4px;
  background-color: #fff;
  color: #666;
  cursor: pointer;
  transition: all 0.3s;
  font-size: 14px;
}

.tabs button.active {
  background-color: #4CAF50;
  color: white;
}

.content {
  background-color: #fff;
  border-radius: 8px;
  padding: 1rem;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  flex: 1;
  display: flex;
  flex-direction: column;
  width: 100%;
}
</style>
