<template>
  <div class="container">
    <h1 class="display-3 text-center mb-4">Vue Todo</h1>
    <form @submit.prevent="addTodo" class="mb-3">
      <div class="input-group">
        <input type="text" class="form-control" placeholder="Add a new todo..." v-model="newTodo">
        <button class="btn btn-primary" type="submit">Add</button>
      </div>
    </form>
    
    <div class=""> <!-- Add this wrapper for responsive behavior -->
      <div class="grid col-12">
  <div class="row">
    <div class="col-1">Mark</div>
    <div class="col-5">Task</div>
    <div class="col-2">Created At</div>
    <div class="col-2">Updated At</div>
    <div class="col-1">Edit Task</div>
    <div class="col-1">Delete Task</div>
  </div>
</div>
      <SingleTodo v-for="(todo, index) in todos" :key="index" :todo="todo" @delete="deleteTodo" @toggle="toggleTodo" @edit="editTodo" />

    </div>
  </div>
</template>
<script>
import SingleTodo from "./SingleTodo.vue";
import { nanoid } from "nanoid";
import axios from "axios";

export default {
  components: {
    SingleTodo,
  },
  data() {
    return {
      todos: [],
      newTodo: "",
    };
  },
  mounted() {
    this.fetchTodos();
  },
  methods: {
    fetchTodos() {
      axios
        .get("http://localhost:3000/todos")
        .then((response) => {
          this.todos = response.data;
        })
        .catch((error) => {
          console.error("Error fetching todos:", error);
        });
    },
    addTodo() {
      if (this.newTodo.trim() !== "") {
        const newTodo = {
          id: nanoid(),
          creationDate: new Date().toISOString(),
          updateDate: new Date().toISOString(),
          completed: false,
          todoString: this.newTodo,
        };
        axios
          .post("http://localhost:3000/todos", newTodo)
          .then(() => {
            this.todos.push(newTodo);
            this.newTodo = "";
          })
          .catch((error) => {
            console.error("Error adding todo:", error);
          });
      }
    },
    deleteTodo(id) {
      // Remove the todo from the todos array immediately
      this.todos = this.todos.filter((todo) => todo.id !== id);

      // Make HTTP request to delete the todo from the server
      axios.delete(`http://localhost:3000/todos/${id}`).catch((error) => {
        console.error("Error deleting todo:", error);
      });
    },
    toggleTodo(id) {
      const todo = this.todos.find((todo) => todo.id === id);
      if (todo) {
        todo.completed = !todo.completed;
        todo.updateDate = new Date().toISOString();
        axios
          .patch(`http://localhost:3000/todos/${id}`, {
            completed: todo.completed,
            updateDate: todo.updateDate,
          })
          .catch((error) => {
            console.error("Error toggling todo:", error);
          });
      }
    },
    editTodo({ id, todoString }) {
      const todo = this.todos.find((todo) => todo.id === id);
      if (todo) {
        todo.todoString = todoString;
        todo.updateDate = new Date().toISOString();
        axios
          .patch(`http://localhost:3000/todos/${id}`, {
            todoString: todoString,
            updateDate: todo.updateDate,
          })
          .catch((error) => {
            console.error("Error editing todo:", error);
          });
      }
    },
  },

};
</script>
<style>
/* Add your custom styles here */
</style>
