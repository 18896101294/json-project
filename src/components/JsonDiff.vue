<template>
  <div class="json-diff">
    <div class="diff-container">
      <div class="editor-wrapper">
        <h3>原始 JSON</h3>
        <div class="editor" ref="originalEditor"></div>
      </div>
      <div class="editor-wrapper">
        <h3>对比 JSON</h3>
        <div class="editor" ref="compareEditor"></div>
      </div>
    </div>
    <div class="toolbar">
      <button @click="compareJson">对比</button>
      <button @click="clearDiff">清理</button>
    </div>
    <div v-if="message.show" :class="['message', message.type]">
      {{ message.text }}
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue'
import * as monaco from 'monaco-editor'

const originalEditor = ref(null)
const compareEditor = ref(null)
let editor1 = null
let editor2 = null
const message = ref({
  show: false,
  text: '',
  type: 'success',
})

const showMessage = (text, type = 'success') => {
  message.value = {
    show: true,
    text,
    type,
  }
  setTimeout(() => {
    message.value.show = false
  }, 3000)
}

onMounted(() => {
  editor1 = monaco.editor.create(originalEditor.value, {
    value: '',
    language: 'json',
    theme: 'vs',
    automaticLayout: true,
    minimap: { enabled: false },
    fontSize: 14,
    lineNumbers: 'on',
    scrollBeyondLastLine: false,
    wordWrap: 'on',
  })

  editor2 = monaco.editor.create(compareEditor.value, {
    value: '',
    language: 'json',
    theme: 'vs',
    automaticLayout: true,
    minimap: { enabled: false },
    fontSize: 14,
    lineNumbers: 'on',
    scrollBeyondLastLine: false,
    wordWrap: 'on',
  })
})

onBeforeUnmount(() => {
  if (editor1) editor1.dispose()
  if (editor2) editor2.dispose()
})

const compareJson = () => {
  try {
    const json1 = editor1.getValue()
    const json2 = editor2.getValue()

    if (!json1 || !json2) {
      showMessage('请确保两个编辑器都有内容', 'error')
      return
    }

    const obj1 = JSON.parse(json1)
    const obj2 = JSON.parse(json2)

    // 直接设置格式化后的 JSON 到两个编辑器
    editor1.setValue(JSON.stringify(obj1, null, 2))
    editor2.setValue(JSON.stringify(obj2, null, 2))

    // 创建差异模型
    const originalModel = editor1.getModel()
    const modifiedModel = editor2.getModel()

    // 设置差异编辑器
    const diffEditor = monaco.editor.createDiffEditor(originalEditor.value.parentElement, {
      automaticLayout: true,
      enableSplitViewResizing: true,
      renderSideBySide: true,
    })

    diffEditor.setModel({
      original: originalModel,
      modified: modifiedModel,
    })

    showMessage('对比完成')
  } catch (error) {
    showMessage('JSON 格式无效', 'error')
  }
}

const clearDiff = () => {
  editor1.setValue('')
  editor2.setValue('')
  showMessage('已清理')
}
</script>

<style scoped>
.json-diff {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  position: relative;
  min-height: 500px;
}

.diff-container {
  flex: 1;
  display: flex;
  gap: 20px;
  margin-bottom: 10px;
  width: 100%;
}

.editor-wrapper {
  flex: 1;
  display: flex;
  flex-direction: column;
  min-width: 0;
  width: 100%;
}

.editor {
  flex: 1;
  border: 1px solid #ddd;
  border-radius: 4px;
  overflow: hidden;
  width: 100%;
}

h3 {
  margin: 0 0 10px 0;
  color: #333;
  font-size: 14px;
  font-weight: 600;
}

.toolbar {
  display: flex;
  gap: 10px;
  flex-wrap: wrap;
  width: 100%;
}

button {
  padding: 8px 16px;
  border: none;
  border-radius: 4px;
  background-color: #4caf50;
  color: white;
  cursor: pointer;
  transition: background-color 0.3s;
  font-size: 14px;
}

button:hover {
  background-color: #45a049;
}

.message {
  position: fixed;
  top: 20px;
  right: 20px;
  padding: 10px 20px;
  border-radius: 4px;
  color: white;
  animation: fadeIn 0.3s ease;
  z-index: 1000;
}

.message.success {
  background-color: #4caf50;
}

.message.error {
  background-color: #f44336;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(-10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
</style>
