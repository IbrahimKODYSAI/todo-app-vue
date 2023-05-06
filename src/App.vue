<script setup>
import { ref, onMounted, computed, watch } from "vue";
import Counter from "./components/Counter/Counter.vue";

const todos = ref([]);
const name = ref("");

const input_content = ref("");
const input_category = ref(null);

const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
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
      this.$nextTick(() => this.$refs.input);
    },
    stopEditing() {
      this.isEditing = false;
    },
  },
};
</script>

<template>
  <main
    class="app w-[100vw] max-w-[1000px] m-auto rounded-md p-2 min-h-[100vh] border-2 border-green-500 pb-20"
  >
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
              <p
                class="words-break w-full overflow-hidden max-h-[75px] cursor-pointer overflow-ellipsis"
              >
                {{ todo.content || "Type your task please" }}
              </p>
            </template>
            <template v-else>
              <input
                type="text"
                v-model="todo.content"
                class="overflow-ellipsis"
                ref="input"
                @blur="stopEditing"
              />
            </template>
          </div>
          <div class="actions">
            <button @click="removeTodo(todo)">
              <i
                class="fa-solid fa-trash text-[#ff5b57] cursor-pointer text-xl"
              ></i>
            </button>
          </div>
        </div>
      </div>
    </section>
  </main>
  <footer>
    <div class="bg-black py-4 text-gray-600 text-sm text-center">
      <p>Todo Genius</p>
      <p>all right reserved® Kody saneda 2023</p>
    </div>
  </footer>
</template>
