<template>
  <div class="todo-app">
    <div class="title">
      <h1>Task Pilot</h1>
      <ph-paper-plane-tilt :size="80" color="#fff" />
    </div>
    <form @submit.prevent="addTodo" class="add-todo">
      <h2 v-if="!hasTodos">Add a list of Todos</h2>
      <h2 v-else>Beat procrastination, get started now!</h2>
      <input type="text" v-model="newTodo" placeholder="Add a new task" />
      <button class="add-btn" :disabled="isNewTodoEmpty">Add Todo</button>
    </form>
    <ul class="todo-list">
      <li v-for="(todo, index) in activeTodos" :key="index" class="todo-item">
        <button @click="toggleTodoStatus(todo)">
          <ph-circle v-if="!todo.isCompleted" :size="16" color="#f98525" />
          <ph-check v-else :size="16" color="#f98525" />
        </button>
        <div>
          <p
            v-if="!todo.isEditing"
            :class="{ completed: todo.isCompleted }"
            class="todo-name"
          >
            {{ todo.name }}
          </p>
          <input
            v-else
            class="edit-input"
            type="text"
            v-model="todo.name"
            @blur="updateTodo(todo)"
            @keyup.enter="updateTodo(todo)"
          />
        </div>
        <button class="edit-btn" @click="toggleEdit(todo)">
          <ph-pencil-simple-line
            v-if="!todo.isEditing"
            :size="16"
            color="#f98525"
          />
          <ph-file-plus v-else :size="16" color="#f98525" />
        </button>
        <button class="delete-btn" @click="removeTodo(todo)">
          <ph-trash :size="16" color="red" />
        </button>
      </li>
    </ul>

    <footer>
      <i
        >Write your todo task and press "Enter" or click the "Add Todo"
        button.</i
      >
    </footer>
  </div>
</template>

<script>
export default {
  name: "TodoApp",
  data() {
    return {
      newTodo: "",
      todos: [],
    };
  },
  computed: {
    activeTodos() {
      return this.todos.filter((todo) => !todo.deleted);
    },
    hasTodos() {
      return this.activeTodos.length > 2;
    },
    isNewTodoEmpty() {
      return !this.newTodo.trim();
    },
  },
  methods: {
    addTodo() {
      if (!this.isNewTodoEmpty) {
        const id = this.todos.length + 1;
        const todo = {
          id,
          name: this.newTodo,
          isCompleted: false,
          deleted: false,
          isEditing: false,
        };

        this.todos.unshift(todo);
        this.newTodo = "";
      }
    },
    removeTodo(todo) {
      todo.deleted = true;
    },
    toggleTodoStatus(todo) {
      todo.isCompleted = !todo.isCompleted;
    },
    toggleEdit(todo) {
      todo.isEditing = !todo.isEditing;
    },
    updateTodo(todo) {
      todo.isEditing = false;
      const index = this.todos.findIndex((t) => t.id === todo.id);

      if (index !== -1) {
        this.$set(this.todos, index, todo);
      }
    },
  },
};
</script>

<script setup>
import {
  PhPaperPlaneTilt,
  PhCircle,
  PhCheck,
  PhPencilSimpleLine,
  PhFilePlus,
  PhTrash,
} from "@phosphor-icons/vue";
</script>

<!-- <style>
.todo-app {
  text-align: center;
}

.title {
  margin-bottom: 15px;
  font-size: 18px;
  font-weight: 600;
}

.add-btn {
  margin-left: 5px;
}

.edit-btn {
  margin-left: 5px;
}

.delete-btn {
  margin-right: 5px;
  margin-left: 20px;
}

.input-container {
  margin-top: 20px;
}

.todo-item {
  margin-bottom: 10px;
}

.has-line-through {
  text-decoration: line-through;
}

.empty-message {
  margin-top: 10px;
  color: #888;
}
</style> -->

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
