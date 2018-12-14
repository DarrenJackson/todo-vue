<template>
  <div class="todo-clear-completed">
    <transition name="fade">
      <button v-if="showClearCompletedButton" @click="clearCompleted">Clear Completed</button>
    </transition>
  </div>
</template>

<script>
export default {
  name: 'todo-clear-completed'  ,
  props: {
    showClearCompletedButton: {
      type: Boolean,
      required: true
    }
  },
  methods: {
    clearCompleted() {
      eventBus.$emit('clearCompletedEvent')
    }
  }
}
</script>

