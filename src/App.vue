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
      <button class="add-btn" :disabled="isNewTodoEmpty" title="Add a new task">
        Add Todo
      </button>
    </form>

    <ul class="todo-list">
      <button
        :class="['delete-all-btn', { hidden: !hasTodos }]"
        @click="deleteAllTodos"
        title="Clear all tasks?"
      >
        Delete All
      </button>
      <transition-group name="fade" tag="div">
        <li v-for="(todo, index) in activeTodos" :key="index" class="todo-item">
          <button
            v-if="!todo.isCompleted"
            class="checkbox-btn"
            @click="toggleTodoStatus(todo)"
            title="Mark as complete"
          ></button>
          <button
            v-else
            class="checkbox-btn"
            @click="toggleTodoStatus(todo)"
            title="Mark as not complete"
          >
            <ph-check :size="12" color="#234e70" weight="bold" />
          </button>
          <div class="todo">
            <p
              v-if="!todo.isEditing"
              :class="['todo-name', { completed: todo.isCompleted }]"
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
            :class="['edit-btn', { hidden: todo.isCompleted }]"
            @click="toggleEdit(todo)"
          >
            <ph-pencil-simple-line
              v-if="!todo.isEditing"
              :size="20"
              color="#f98525"
            >
              <animate
                attributeName="opacity"
                values="0.7;1;0.7"
                dur="4s"
                repeatCount="indefinite"
              />
            </ph-pencil-simple-line>

            <ph-check v-else :size="20" color="#f98525">
              <animate
                attributeName="opacity"
                values="0.7;1;0.7"
                dur="4s"
                repeatCount="indefinite"
              />
            </ph-check>
          </button>
          <button
            v-if="!todo.isEditing"
            class="delete-btn"
            @click="deleteTodo(todo)"
          >
            <ph-trash :size="20" color="red">
              <animate
                attributeName="opacity"
                values="0.7;1;0.7"
                dur="4s"
                repeatCount="indefinite"
            /></ph-trash>
          </button>
          <button v-else @click="cancelEdit(todo)">
            <ph-prohibit :size="20" color="red">
              <animate
                attributeName="opacity"
                values="0.7;1;0.7"
                dur="4s"
                repeatCount="indefinite"
            /></ph-prohibit>
          </button>
        </li>
      </transition-group>
    </ul>

    <footer>
      <i v-if="!hasTodos"
        >Write your todo task and press "Enter" or click the "Add Todo"
        button.</i
      >
      <p v-else>
        {{ message }}
      </p>
    </footer>
  </div>
</template>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter,
.fade-leave-to {
  opacity: 0;
}
</style>

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
      return this.activeTodos.length > 1;
    },
    isNewTodoEmpty() {
      return !this.newTodo.trim();
    },
    numTodos() {
      return this.activeTodos.length;
    },
    numCompleted() {
      return this.activeTodos.filter((todo) => todo.isCompleted).length;
    },
    percentage() {
      return Math.round((this.numCompleted / this.numTodos) * 100);
    },
    message() {
      return this.percentage === 100
        ? "All task completed! You are awesome 🤩"
        : `You have ${this.numTodos} tasks todo on your list, and you've completed ${this.numCompleted} (${this.percentage}%)`;
    },
  },
  methods: {
    saveTodosToLocalStorage() {
      localStorage.setItem("todos", JSON.stringify(this.todos));
    },
    getTodosFromLocalStorage() {
      const todos = localStorage.getItem("todos");
      return todos ? JSON.parse(todos) : [];
    },

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
        this.saveTodosToLocalStorage();
      }
    },
    deleteAllTodos() {
      this.todos = [];
      this.saveTodosToLocalStorage();
    },
    deleteTodo(todo) {
      todo.deleted = true;
      this.saveTodosToLocalStorage();
    },
    toggleTodoStatus(todo) {
      todo.isCompleted = !todo.isCompleted;
      this.saveTodosToLocalStorage();
    },
    toggleEdit(todo) {
      todo.isEditing = !todo.isEditing;
    },

    updateTodo(todo) {
      todo.isEditing = false;
      const index = this.todos.findIndex((t) => t.id === todo.id);

      if (index !== -1) {
        this.$set(this.todos, index, todo);
        this.saveTodosToLocalStorage();
      }
    },

    cancelEdit(todo) {
      todo.isEditing = false;
      return todo.name;
    },
  },
  created() {
    this.todos = this.getTodosFromLocalStorage();
  },
};
</script>

<script setup>
import {
  PhCheck,
  PhPencilSimpleLine,
  PhTrash,
  PhProhibit,
} from "@phosphor-icons/vue";

import { ref, onMounted } from "vue";
import autoAnimate from "@formkit/auto-animate";

const dropdown = ref();
const show = ref(false);

onMounted(() => {
  autoAnimate(dropdown.value);
});
</script>
