<template>
  <div>
      <input type="text" class="todo-input" placeholder="What needs to be done" v-model="newTodo" @keyup.enter="addToDo">

      <transition-group enter-active-class="animated slideInLeft" leave-active-class="animated slideOutRight">
      <todo-item v-for="(todo, index) in todosFiltered" :key="todo.id" 
        :todo="todo" :index="index" :checkAll="!anyRemaining">

      </todo-item>
      </transition-group>

      <div class="extra-container">
        <todo-check-all :anyRemaining="anyRemaining"></todo-check-all>
        <todo-items-remaining :remaining="remaining"></todo-items-remaining>
      </div>

      <div class="extra-container">
        <todo-filter></todo-filter>
         <!-- <div>
          <button :class="{ active: filter == 'all' }" @click="filter = 'all'">All</button>
          <button :class="{ active: filter == 'active' }" @click="filter = 'active'">Active</button>
          <button :class="{ active: filter == 'completed' }" @click="filter = 'completed'">Completed</button>
        </div> -->
        <todo-clear-completed :showClearCompletedButton="showClearCompletedButton"></todo-clear-completed>
        <!-- <div>
          <transition name="fade">
            <button v-if="showClearCompletedButton" @click="clearCompleted">Clear Completed</button>
          </transition>
        </div> -->
      </div>
  </div>
</template>

<script>
import TodoItem from './TodoItem'
import TodoItemsRemaining from './TodoItemsRemaining'
import TodoCheckAll from './TodoCheckAll'
import TodoFilter from './TodoFilter'
import TodoClearCompleted from './TodoClearCompleted'
export default {
  name: 'todo-list',
  components: {
    TodoItem,
    TodoItemsRemaining,
    TodoCheckAll,
    TodoFilter,
    TodoClearCompleted
  },
  data () {
    return {
      newTodo: '',
      idForTodo: 3,
      beforeEditCache: '',
      filter: 'all',
      todos: [
        {
          'id': 1,
          'title': 'Finish screencast',
          'completed': false,
          'editing': false
        },
        {
          'id': 2,
          'title': 'take over world yeah',
          'completed': false,
          'editing': false
        },
      ]
    }
  },
  created() {
    eventBus.$on('removedTodo', index => this.removeTodo(index))
    eventBus.$on('finishedEdit', data => this.finishedEdit(data))
    eventBus.$on('checkAllTodosEvent', this.checkAllTodos)
    eventBus.$on('filterChanged', filter => this.filter = filter)
    eventBus.$on('clearCompletedEvent', this.clearCompleted)
  },
  computed: {
    remaining() {
      return this.todos.filter(todo => !todo.completed).length;
    },

    anyRemaining() {
      return this.remaining != 0;
    },
    todosFiltered() {
      if (this.filter == 'all') {
        return this.todos
      } else 
      if (this.filter == 'active') {
        return this.todos.filter(todo => !todo.completed)
      } else 
      if (this.filter == 'completed') {
        return this.todos.filter(todo => todo.completed)
      }

      return this.todos
    },
    showClearCompletedButton() {
      return this.todos.filter(todo => todo.completed).length > 0
    }

  },
  directives: {
    focus: {
      inserted: function (el)
      {
        el.focus()
      }
    }
  },
  methods: {
    addToDo() {
      if (this.newTodo.trim().length == 0) {
        return
      }

      this.todos.push({
        id: this.idForTodo,
        title: this.newTodo,
        completed: false
      })

      this.newTodo = ''
      this.idForTodo++
    },
    removeTodo(index) {
      this.todos.splice(index, 1)
    },
    checkAllTodos(isChecked) {
      this.todos.forEach(x => x.completed = isChecked)
    },
    clearCompleted() {
      this.todos = this.todos.filter(todo => !todo.completed)  
    },
    finishedEdit(data) {
        this.todos.splice(data.index, 1, data.todo);
    },
  }
}
</script>

<style lang="scss">
  @import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.7.0/animate.min.css");

  .todo-input {
    width: 100%;
    padding: 10px 18px;
    font-size: 18px;
    margin-bottom: 16px;

    &:focus {
      outline: 0;
    }
  }

  .todo-item {
    margin-bottom:12px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    animation-duration: 0.3s;
  }

  .remove-item {
    cursor: pointer;
    margin-left: 14px;
    &:hover {
      color: black;
    }
  }

  .todo-item-left {
    display: flex;
    align-items: center;
  }

  .todo-item-label {
    padding: 10px;
    border: 1px solid white;
    margin-left: 12px;
  }

  .todo-item-edit {
    font-size: 24px;
    color: #2c3e50;
    margin-left: 12px;
    width: 100%;
    padding: 10px;
    border: 1px solid #ccc;
    font-family: 'Avenir', Arial, Helvetica, sans-serif;

    &:focus {
      outline: 0;
    }
  }

  .completed {
    text-decoration: line-through;
    color: grey;
  }

  .extra-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 16px;
    border-top: 1px solid lightgray;
    padding-top: 14px;
    margin-bottom: 14px;
  }

  button {
    font-size: 14px;
    background-color: white;
    appearance: none;
    border: #ccc;

    &:hover {
      background: lightgreen;
    }

    &:focus {
      outline: none;
    }

  }

  .active {
    background: lightgreen;
  }

  .fade-enter-active, .fade-leave-active {
    transition: opacity .2s;
  }

  .fade-enter, .fade-leave-to {
    opacity: 0;
  }
</style>


