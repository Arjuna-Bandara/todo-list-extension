<template>
  <div>
    <section class="todoapp">
      <header class="header">
        <h1>To Do List</h1>
        <input class="new-todo"
          autofocus autocomplete="off"
          placeholder="What needs to be done?"
          v-model="newTodo"
          @keyup.enter="addTodo">
      </header>
      <section class="main" v-show="todos.length" >
        <input id="toggle-all" class="toggle-all" type="checkbox" v-model="allDone">
        <label for="toggle-all"></label>
        <ul class="todo-list">
          <li v-for="todo in filteredTodos"
            class="todo"
            :key="todo.id"
            :class="{ completed: todo.completed, editing: todo == editedTodo }">
            <div class="view">
              <input class="toggle" type="checkbox" v-model="todo.completed">
              <label @dblclick="editTodo(todo)">{{ todo.title }}</label>
              <button class="destroy" @click="removeTodo(todo)"></button>
            </div>
            <input class="edit" type="text"
              v-model="todo.title" v-todo-focus="todo == editedTodo"
              @blur="doneEdit(todo)"
              @keyup.enter="doneEdit(todo)"
              @keyup.esc="cancelEdit(todo)" />
          </li>
        </ul>
      </section>
      <footer class="footer" v-show="todos.length">
        <span class="todo-count">
          <strong>{{ remaining }}</strong> {{ remaining | pluralize }} left
        </span>
        <ul class="filters">
          <li><a href="#" @click="filterTodos('all')" :class="{ selected: visibility == 'all' }">All</a></li>
          <li><a href="#" @click="filterTodos('active')" :class="{ selected: visibility == 'active' }">Active</a></li>
          <li><a href="#" @click="filterTodos('completed')" :class="{ selected: visibility == 'completed' }">Completed</a></li>
        </ul>
        <button class="clear-completed" @click="removeCompleted" v-show="todos.length > remaining">
          Clear completed
        </button>
      </footer>
    </section>
    <footer class="info">
      <p>Double-click to edit a todo</p>
      <p>Written by <a href="http://evanyou.me">Evan You</a></p>
      <p>Part of <a href="http://todomvc.com">TodoMVC</a></p>
    </footer>
  </div>
</template>

<script>
export default {
    name: 'TodoList',
    data() {
        return {
            newTodo: null,
            todos: [],
            filteredTodos: [],
            visibility: 'all',
            editedTodo: null,
            STORAGE_KEY: 'todo-list-v2'
        }
    },
    computed: {
        remaining: function() {
            return this.todos.filter(todo => !todo.completed).length
        },
        allDone: {
            get: function() {
                return this.remaining === 0
            },
            set: function(value) {
                this.todos.map(todo => todo.completed = value)

                this.listTodos()
            }
        }
    },
    mounted() {

        this.todos = JSON.parse(localStorage.getItem(this.STORAGE_KEY)) || []

        this.listTodos()
    },
    methods: {
        listTodos() {

            this.filteredTodos = []

            if (this.visibility == 'all') {
                this.todos.forEach(todo => {
                    this.filteredTodos.push(todo)
                })
            } else if(this.visibility == 'active') {
                this.todos.filter(todo => !todo.completed).forEach(todo => {
                    this.filteredTodos.push(todo)
                })
            } else if(this.visibility == 'completed') {
                this.todos.filter(todo => todo.completed).forEach(todo => {
                    this.filteredTodos.push(todo)
                })
            }
        },
        addTodo() {
            this.todos.push({
                id: this.todos.length + 1,
                title: this.newTodo,
                completed: false
            })

            localStorage.setItem(this.STORAGE_KEY, JSON.stringify(this.todos))

            this.listTodos()

            this.newTodo = null
        },
        editTodo(todo) {
            this.editedTodo = todo
        },
        removeTodo(data) {
            this.todos = this.todos.filter(todo => todo.id != data.id)

            localStorage.setItem(this.STORAGE_KEY, JSON.stringify(this.todos))

            this.listTodos()
        },
        doneEdit() {

            localStorage.setItem(this.STORAGE_KEY, JSON.stringify(this.todos))

            this.editedTodo = null
        },
        cancelEdit() {
            this.editedTodo = null
        },
        removeCompleted() {
            this.todos = this.todos.filter(todo => !todo.completed)

            localStorage.setItem(this.STORAGE_KEY, JSON.stringify(this.todos))

            this.listTodos()
        },
        filterTodos(type) {

            this.visibility = type

            this.listTodos()
        }
    },
    filters: {
        pluralize: function (n) {
            if (n <= 0) {
                return 'item'
            } else if(n === 1) {
                return 'item'
            }

            return n === 1 ? 'item' : 'items'
        }
    },
    directives: {
        'todo-focus': function (el, binding) {
            if (binding.value) {
                el.focus()
            }
        }
    }
}
</script>
