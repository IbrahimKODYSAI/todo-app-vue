<script setup>
import { ref, onMounted, computed, watch } from "vue";
import Counter from "./components/Counter/Counter.vue";

const todos = ref([]);
const name = ref("");

const input_content = ref("");
const input_category = ref(null);

const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return a.createdAt - b.createdAt;
  })
);

function generateUniqueId() {
  return Math.random().toString(36).substring(2) + Date.now().toString(36);
}

const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }
  todos.value.push({
    Id: generateUniqueId(),
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime(),
  });
  input_content.value = "";
  input_category.value = null;
};

const removeTodo = (todo) => {
  todos.value = todos.value.filter((item) => item != todo);
};

watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  { deep: true }
);

watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos") || []);
});
</script>
<script>
export default {
  props: {
    todo: Object,
  },
  data() {
    return {
      isEditing: false,
    };
  },
  methods: {
    editContent() {
      this.isEditing = true;
      this.$nextTick(() => this.$refs.textarea.focus());
    },
    stopEditing() {
      this.isEditing = false;
    },
    resizeTextarea() {
      this.$refs.textarea.style.height = "auto";
      this.$refs.textarea.style.height = `${this.$refs.textarea.scrollHeight}px`;
    },
  },
};
</script>

<template>
  <main class="app w-[100vw] max-w-[1000px] m-auto rounded-md p-2 h-[100vh]">
    <section class="greeting">
      <h2 class="title">
        What's up, <input type="text" placeholder="Name here" v-model="name" />
      </h2>
    </section>
    <section class="create-todo">
      <h3>CREATE A TODO</h3>
      <form @submit.prevent="addTodo">
        <h4>What's on your todo list ?</h4>
        <input type="text" placeholder=" Go to gym" v-model="input_content" />

        <h4>Pick a category</h4>
        <div class="options">
          <label>
            <input
              type="radio"
              name="category1"
              value="business"
              v-model="input_category"
            />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>
          <label>
            <input
              type="radio"
              name="category1"
              value="personal"
              v-model="input_category"
            />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <input type="submit" value="Add todo" />
      </form>
    </section>
    <section class="todo-list">
      <div class="flex text-white justify-between">
        <h3>TODO LIST</h3>
        <Counter :count="todos.length" />
      </div>
      <div class="todo-list">
        <div
          v-for="todo in todos_asc"
          :key="todo.Id"
          :class="`todo-item ${todo.done && 'done'}`"
        >
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          <div class="todo-content" @click="editContent">
            <template v-if="!isEditing">
              <span class="break-words overflow-ellipsis">{{
                todo.content
              }}</span>
            </template>
            <template v-else>
              <textarea
                v-model="todo.content"
                ref="textarea"
                rows="1"
                max-rows="6"
                @blur="stopEditing"
                @input="resizeTextarea"
              ></textarea>
            </template>
          </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
