<template>
  <n-tabs class="all-box" size="large" animated>
    <!-- 捷径 -->
    <n-tab-pane
      class="tab-pane"
      name="link"
      tab="捷径"
      :tab-style="{ justifyContent: 'flex-start', display: 'flex' }"
    >
      <ShortCut />
    </n-tab-pane>

    <!-- 便签 -->
    <n-tab-pane
      class="tab-pane"
      name="note"
      tab="便签"
      :tab-style="{ justifyContent: 'center', display: 'flex' }"
    >
      <div class="note-box">
        <n-input v-model:value="noteSearch" placeholder="搜索便签..." clearable />
        <n-input
          v-model:value="newNote"
          placeholder="写下你的便签..."
          @keyup.enter="addNote"
          clearable
          class="mt-2"
        />
        <n-button class="mt-2" type="primary" @click="addNote">添加便签</n-button>

        <n-list class="mt-4" bordered>
          <n-list-item v-for="(note, index) in filteredNotes" :key="index">
            <div class="note-item">
              <n-input
                v-model:value="notes[index]"
                type="text"
                @blur="saveNotes"
                @keyup.enter="onEnterBlur"
                style="flex: 1;"
              />
              <n-button size="tiny" type="error" @click="removeNote(index)">删除</n-button>
            </div>
          </n-list-item>
        </n-list>
      </div>
    </n-tab-pane>

    <!-- 待办 -->
    <n-tab-pane
      class="tab-pane"
      name="more"
      tab="待办"
      :tab-style="{ justifyContent: 'flex-end', display: 'flex' }"
    >
      <div class="todo-box">
        <n-input v-model:value="todoSearch" placeholder="搜索待办..." clearable />
        <n-input
          v-model:value="newTodo"
          placeholder="添加待办事项..."
          @keyup.enter="addTodo"
          clearable
          class="mt-2"
        />
        <n-button class="mt-2" type="success" @click="addTodo">添加待办</n-button>

        <n-list class="mt-4" bordered>
          <n-list-item
            v-for="(todo, index) in filteredTodos"
            :key="index"
          >
            <div class="todo-item">
              <n-checkbox
                v-model:checked="todo.done"
                @update:checked="saveTodos"
              />
              <n-input
                v-model:value="todo.text"
                @blur="saveTodos"
                @keyup.enter="onEnterBlur"
                style="flex: 1; margin: 0 8px;"
              />
              <n-button size="tiny" type="error" @click="removeTodo(index)">删除</n-button>
            </div>
          </n-list-item>
        </n-list>
      </div>
    </n-tab-pane>
  </n-tabs>
</template>

<script setup>
import { ref, computed, onMounted, watch } from "vue";
import {
  NTabs,
  NTabPane,
  NInput,
  NButton,
  NList,
  NListItem,
  NCheckbox,
} from "naive-ui";
import ShortCut from "@/components/AllFunc/Box/ShortCut.vue";

// Storage keys
const NOTE_KEY = "my_notes";
const TODO_KEY = "my_todos";

// ====== 便签功能 ======
const newNote = ref("");
const noteSearch = ref("");
const notes = ref([]);

const addNote = () => {
  if (newNote.value.trim()) {
    notes.value.push(newNote.value.trim());
    newNote.value = "";
    saveNotes();
  }
};
const removeNote = (index) => {
  notes.value.splice(index, 1);
  saveNotes();
};
const saveNotes = () => {
  localStorage.setItem(NOTE_KEY, JSON.stringify(notes.value));
};
const filteredNotes = computed(() =>
  notes.value.filter((n) =>
    n.toLowerCase().includes(noteSearch.value.trim().toLowerCase())
  )
);

// ====== 待办功能 ======
const newTodo = ref("");
const todoSearch = ref("");
const todos = ref([]);

const addTodo = () => {
  if (newTodo.value.trim()) {
    todos.value.push({ text: newTodo.value.trim(), done: false });
    newTodo.value = "";
    saveTodos();
  }
};
const removeTodo = (index) => {
  todos.value.splice(index, 1);
  saveTodos();
};
const saveTodos = () => {
  localStorage.setItem(TODO_KEY, JSON.stringify(todos.value));
};
const filteredTodos = computed(() =>
  [...todos.value]
    .filter((todo) =>
      todo.text.toLowerCase().includes(todoSearch.value.trim().toLowerCase())
    )
    .sort((a, b) => a.done - b.done)
);

// 回车失焦
const onEnterBlur = (event) => {
  event.target.blur();
};

// 初始化加载本地数据
onMounted(() => {
  const savedNotes = localStorage.getItem(NOTE_KEY);
  if (savedNotes) notes.value = JSON.parse(savedNotes);

  const savedTodos = localStorage.getItem(TODO_KEY);
  if (savedTodos) todos.value = JSON.parse(savedTodos);
});

// 自动保存
watch(notes, saveNotes, { deep: true });
watch(todos, saveTodos, { deep: true });
</script>

<style>
html,
body,
#app {
  height: 100%;
  margin: 0;
}

.all-box {
  height: 100%;
}

.tab-pane {
  height: 100%;
  padding: 16px;
  box-sizing: border-box;
  overflow-y: auto;
}

.note-box,
.todo-box {
  max-width: 600px;
  margin: 0 auto;
}

.note-item,
.todo-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 8px;
}
</style>
