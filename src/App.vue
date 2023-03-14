<template>
  <div class="todos">
    <h1>Ma Todo List</h1>
    <div>
      <input v-model="newTodo" placeholder="Ajouter une tâche">
      <button @click="addTodo">Ajouter</button>
    </div>
    <ul class="todo-list">
      <li v-for="(todo, index) in todos" :key="index">
        <div :class="{ editing: todo.editing }" v-if="todo.editing">
          <input v-model="todo.text" @keyup.enter="saveEdit(todo)" @keyup.esc="cancelEdit(todo)">
          <button @click="saveEdit(todo, true)">Sauvegarder</button>
          <button @click="saveEdit(todo, false)">Annuler</button>
        </div>

        <div v-else>
          <input type="checkbox" v-model="todo.done" @change="updateStatus(todo)">
          <span :class="{ completed: todo.done }" @click="startEdit(todo)">
            {{ todo.text }}
            <span v-if="todo.done"> (Terminé) </span>
            <span v-else> (En cours) </span>
          </span>
          <button @click="confirmDelete(index)">Supprimer</button>
          <button @click="editMode(todo)">Modifier</button>
        </div>

      </li>
    </ul>
    <div class="btn-all">
      <button @click="deleteCompleted">Supprimer les todos terminés</button>
      <button @click="confirmDeleteAll">Supprimer tous les todos</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newTodo: '',
      todos: []
    }
  },
  methods: {
    addTodo() {
      if (this.newTodo.trim() !== '') {
        this.todos.push({
          text: this.newTodo.trim(),
          completed: false,
          editing: false
        })
        this.newTodo = ''
      }
    },
    updateStatus(todo) {
      todo.completed = !todo.completed
    },
    startEdit(todo) {
      todo.editing = true
      this.$nextTick(() => {
        this.$refs['editField'][0].focus()
      })
    },
    saveEdit(todo, isNewTodo) {
      if (todo.text.trim() === '') {
        this.deleteTodo(this.todos.indexOf(todo))
      } else {
        todo.editing = false
        if (this.todos.indexOf(todo) === -1) {
          this.todos.push({
            text: todo.text.trim(),
            completed: false,
            editing: false
          })
        }
      }
    },
    cancelEdit(todo) {
      todo.editing = false
    },

    confirmDelete(index) {
      if (confirm("Êtes-vous sûr de vouloir supprimer cet élément de la liste ?")) {
        this.deleteTodo(index)
      }
    },
    deleteTodo(index) {
      this.todos.splice(index, 1)
    },
    editMode(todo) {
      todo.editing = true
      this.$nextTick(() => {
        this.$refs['editField'][0].focus()
      })
    },
    confirmDeleteAll() {
      if (confirm("Êtes-vous sûr de vouloir supprimer tous les todos ?")) {
        this.todos = []
      }
    },
    deleteCompleted() {
      if (confirm("Êtes-vous sûr de vouloir supprimer les todos terminés ?")) {
        this.todos = this.todos.filter(todo => !todo.completed)
      }
    }

  }
}

</script>


<style>
.completed {
  text-decoration: line-through;
}

.todos {
  margin: 20px;
}

.todo-list {
  list-style: none;
}

.todo-list>li {
  margin-bottom: 10px;
}
.btn-all {
  margin-top: 15px;
  margin-bottom: 15px;
}
</style>