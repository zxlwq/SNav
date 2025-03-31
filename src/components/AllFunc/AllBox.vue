<template>
  <n-tabs class="all-box" size="large" justify-content="space-evenly" animated>
    <n-tab-pane
      v-for="(tab, index) in tabs"
      :key="tab.name"
      class="no-padding height--full"
      :name="tab.name"
      :tab="tab.label"
    >
      <component :is="tab.component" v-if="tab.component" />
      <span
        v-else
        contenteditable="true"
        class="editable"
        @input="updateContent($event, index)"
      >{{ tab.content }}</span>
    </n-tab-pane>
  </n-tabs>
</template>

<script setup>
import { ref } from "vue";
import { NTabs, NTabPane } from "naive-ui";
import ShortCut from "@/components/AllFunc/Box/ShortCut.vue";

const tabs = ref([
  { name: "link", label: "捷径", component: ShortCut },
  { name: "note", label: "便签", content: "即将完善" },
  { name: "more", label: "待办", content: "还能有啥呢 😢" }
]);

const updateContent = (event, index) => {
  tabs.value[index].content = event.target.innerText;
};
</script>

<style>
.editable {
  display: block;
  min-height: 100px;
  padding: 8px;
  border: 1px solid #ddd;
  border-radius: 4px;
  outline: none;
}
</style>
