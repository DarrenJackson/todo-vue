<template>
  <div class="todo-check-all">
    <label><input type="checkbox" :checked="!anyRemaining" @change="allChecked" >
      Check All
    </label>
  </div>
</template>


<script>
export default {
  name: 'todo-check-all',
  props: {
    anyRemaining: {
      type: Boolean,
      required: true
    }
  },
  methods: {
    allChecked() {
      eventBus.$emit('checkAllTodosEvent', this.anyRemaining)
    }
  }
}
</script>
