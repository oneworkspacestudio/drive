<template>
  <svg class="drive-document-icon" :class="`drive-document-icon--${type}`" viewBox="0 0 24 24" aria-hidden="true">
    <path d="M5 2h9l5 5v15H5z" fill="currentColor"/>
    <path d="M14 2v5h5" fill="#fff" opacity=".35"/>
    <g fill="none" stroke="#fff" stroke-width="1.4" stroke-linecap="round" stroke-linejoin="round">
      <path v-if="type==='docx'" d="M8 11h8M8 14h8M8 17h5"/>
      <path v-else-if="type==='xlsx'" d="M8 10h8v8H8zM8 13h8M8 15.5h8M11 10v8"/>
      <path v-else-if="type==='pptx'" d="M8 10h8v6H8zM12 16v3M9 19h6"/>
      <path v-else d="M8 16c3-5 3-8 4-6 1 2-1 7-3 7-2 0 6-5 7-2 0 2-3-1-4-4"/>
    </g>
  </svg>
</template>
<script setup>
defineProps({type:{type:String,required:true}});
</script>
<style scoped>
.drive-document-icon{width:24px;height:24px;flex:none}.drive-document-icon--docx{color:var(--drive-document-blue)}.drive-document-icon--xlsx{color:var(--drive-document-green)}.drive-document-icon--pptx{color:var(--drive-document-yellow)}.drive-document-icon--pdf{color:var(--drive-document-red)}
</style>
