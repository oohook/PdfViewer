# PdfViewer
[![VUE Version](https://img.shields.io/badge/vue-3-brightgreen.svg)](https://vuejs.org/)
[![Element-Plus Version](https://img.shields.io/badge/elementPlus-2.9-brightgreen.svg)](https://element-plus.org/)

<div align="center">
  <h1>A helpful vue3 + element-plus pdf viewer example.</h1>
  <strong>replica el-image's preview</strong>
</div>

## How to use
- install vue-pdf-embed
  ```bash
  #npm install
  npm install vue-pdf-embed

  # or yarn install
  yarn add vue-pdf-embed
  ```
- add PdfViewer.vue to your components
- copy the following code to your list page(most likely, you need make a simple adjustment before it work in your project)
```bash
#demo.vue
<template>
  <el-card shadow="hover" v-for="(item, index) in list" :key="index">
      # some item code
      <el-button @click="viewRef?.show(index + 1)" link>PreView</el-button>
  </el-card>
  <PdfViewer :pdfList="pdfList" ref="viewRef" />
</template>

<script setup lang="ts">
interface Item {
    name: string;
    url: string;
}
interface dialogFunc {
    show: (param: any) => void
}
const viewRef = ref<dialogFunc | null>(null)
const list = ref<Item[]>([]);
const pdfList = ref<string[]>([])
</script>
```

## Screenshot
<p align="center">
    <img width="900" src="/demo.png">
</p>



