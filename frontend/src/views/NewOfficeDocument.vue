<template>
  <DocumentEditor :new-type="newType" :source="source" :parent-path="parentPath" standalone />
  <Notifications />
</template>

<script>
import DocumentEditor from "@/views/files/DocumentEditor.vue";
import Notifications from "@/components/Notifications.vue";
import { getters } from "@/store";
import { syncDocumentTheme } from "@/utils/theme";

export default {
  components: { DocumentEditor, Notifications },
  props: {
    newType: { type: String, required: true },
    source: { type: String, default: "" },
    parentPath: { type: String, default: "/" },
  },
  computed: {
    darkMode() { return getters.isDarkMode(); },
  },
  watch: {
    darkMode: { immediate: true, handler(dark) { syncDocumentTheme(dark); } },
  },
};
</script>
