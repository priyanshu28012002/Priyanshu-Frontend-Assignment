<template>

  <div class="grid col-12">
  <li class="list-group-item  text-" :class="{ 'completed': todo.completed }">
    <div class="row">
      <div class="col-1">
        <input type="checkbox" class="form-check-input me-2" v-model="isChecked" @change="toggle">
      </div>
      <div class="col-5">
        <span v-if="!editing" @dblclick="startEditing">{{ todo.todoString }}</span>
        <input type="text" class="form-control" v-model="editedText" @keyup.enter="finishEditing" @blur="finishEditing" v-else>
      </div>
      <div class="col-2">
        <p class="max-width: 575.98px" v-if="!editing" @dblclick="startEditing">{{ formatDate(todo.creationDate) }}</p>
      </div>
      <div class="col-2">
        <p v-if="!editing" @dblclick="startEditing">{{ formatDate(todo.updateDate) }}</p>
      </div>
      <div class="col-1">
        <button class="btn btn-warning" @click="editModeToggle">{{ editing ? '' : 'Edit' }}</button>
      </div>
      <div class="col-1">
        <button class="btn btn-danger" @click="deleteTodo">Delete</button>
      </div>
    </div>
  </li>
</div>


</template>

<script>
import axios from 'axios';

export default {
  props: {
    todo: {
      type: Object,
      required: true,
    },
  },
  data() {
    return {
      editing: false,
      editedText: "",
      isChecked: this.todo.completed,
    };
  },
 methods: {
    deleteTodo() {
      axios.delete(`http://localhost:3000/todos/${this.todo.id}`)
        .then(() => {
          this.$emit("delete", this.todo.id);
        })
        .catch(error => {
          console.error("Error deleting todo:", error);
        });
    },
    toggle() {
      this.isChecked = !this.isChecked;
      axios.patch(`http://localhost:3000/todos/${this.todo.id}`, { completed: this.isChecked })
        .then(() => {
          this.$emit("toggle", this.todo.id);
        })
        .catch(error => {
          console.error("Error toggling todo:", error);
        });
    },
    startEditing() {
      this.editing = true;
      this.editedText = this.todo.todoString;
      this.$nextTick(() => this.$el.querySelector('input[type="text"]').focus());
    },
    finishEditing() {
      if (this.editedText.trim() !== "") {
        axios.patch(`http://localhost:3000/todos/${this.todo.id}`, { todoString: this.editedText })
          .then(() => {
            this.$emit("edit", { id: this.todo.id, todoString: this.editedText });
          })
          .catch(error => {
            console.error("Error editing todo:", error);
          });
      }
      this.editing = false;
    },
    editModeToggle() {
      this.isChecked = !this.isChecked;
      if (this.editing) {
        this.finishEditing();
      } else {
        this.startEditing();
      }
    },
    formatDate(dateString) {
      const date = new Date(dateString);
      return date.toLocaleDateString();
    },
  },
};
</script>

<style scoped>
.completed {
  text-decoration: line-through;
}
</style>
