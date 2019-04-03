<template>
  <div>
    <todo-list />
  </div>
</template>

<script>
import TodoList from './components/TodoList.vue'
import './components/TodoList.css'

export default {
  name: 'app',
  components: {
    TodoList
  }
}
</script>