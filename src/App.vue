<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')
const inputContent = ref('')
const inputCategory = ref(null)
const todosAscending = computed(() => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt
}))
const addTodo = () => {
  if (inputContent.value.trim() === '' || inputContent.value === null) {
    return
  }
  todos.value.push({
    content: inputContent.value,
    category: inputCategory.value,
    done: false,
    createdAt: new Date().getTime()
  })
  inputContent.value = ''
  inputCategory.value = null
  // console.log('add to do run')
}
const removeTodo = (todoData) => {
  todos.value = todos.value.filter((todoDatas) => todoDatas !== todoData)
}

watch(todos, (newValue) => {
  localStorage.setItem('todos', JSON.stringify(newValue))
}, { deep: true })

watch(name, (newValue) => {
  localStorage.setItem('name', newValue)
})

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Hi there
          <input type="text" placeholder="name here" v-model="name">
      </h2>
    </section>

    <section class="create-todo">
      <!-- <h3>Create new To Do</h3> -->

      <form @submit.prevent="addTodo">
        <h4>What's on your to do list today, {{ name }}?</h4>
          <input type="text" placeholder="e.g. make a morning coffee" v-model="inputContent">
        <h4>Pick a category</h4>

          <div class="options">
            <label>
              <input type="radio" name="category" value="business" v-model="inputCategory">
              <span class="bubble business"></span>
              <div>Business</div>
            </label>

            <label>
              <input type="radio" name="category" value="personal" v-model="inputCategory">
              <span class="bubble personal"></span>
              <div>Personal</div>
            </label>
          </div>

          <input type="submit" value="Add To Do">
      </form>
    </section>

    <section class="todo-list">
      <!-- <h3>to do list</h3> -->
      <div class="list">
        <div v-for="todosAscendingData in todosAscending" :class="`todo-item ${todosAscendingData.done && 'done'}`">
          <label>
            <input type="checkbox" v-model="todosAscendingData.done">
            <span :class="`bubble ${todosAscendingData.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todosAscendingData.content">
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todosAscendingData)">Delete</button>
          </div>

        </div>
      </div>
    </section>
  </main>
  <!-- {{ name }} = {{ inputCategory }}
  {{ todosAscending }} -->
</template>

<!-- <style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style> -->
