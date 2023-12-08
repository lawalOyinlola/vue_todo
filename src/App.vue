<template>
  <div class="todo-app">
    <header>
      <p class="logo">tp</p>
      <h1>Task Pilot</h1>
    </header>
    <form @submit.prevent="addTodo" class="todo-form">
      <h2 v-if="!hasTodos">Add a list of Todos</h2>
      <h2 v-else>Beat procrastination, get started now!</h2>
      <input
        class="todo-input"
        type="text"
        v-model="newTodo"
        placeholder="Add a new task"
      />
      <button class="add-btn" :disabled="isNewTodoEmpty">Add Todo</button>
    </form>
    <ul class="todo-list">
      <button class="delete-all-btn" v-if="hasTodos" @click="deleteAllTodos">
        Delete All
      </button>
      <li v-for="(todo, index) in activeTodos" :key="index" class="todo-item">
        <button class="checkbox-btn" @click="toggleTodoStatus(todo)">
          <ph-radio-button
            v-if="!todo.isCompleted"
            :size="16"
            color="#f98525"
            weight="bold"
          />
          <ph-check v-else :size="16" color="#f98525" weight="bold" />
        </button>
        <div class="todo">
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
        <button
          v-if="!todo.isCompleted"
          class="edit-btn"
          @click="toggleEdit(todo)"
        >
          <ph-pencil-simple-line
            v-if="!todo.isEditing && !todo.isCompleted"
            :size="16"
            color="#f98525"
          />
          <ph-file-plus v-else-if="todo.isEditing" :size="16" color="#f98525" />
        </button>
        <button class="delete-btn" @click="deleteTodo(todo)">
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
    deleteTodo(todo) {
      todo.deleted = true;
    },
    deleteAllTodos() {
      this.todos = [];
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
  PhRadioButton,
  PhCheck,
  PhPencilSimpleLine,
  PhFilePlus,
  PhTrash,
} from "@phosphor-icons/vue";
</script>
