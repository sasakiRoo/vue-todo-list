<script setup>
import {ref, onMounted, computed, watch} from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt
}))

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) return
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    complete: false,
    createdAt: new Date().getTime()
  }) 
  
  input_content.value = ''
  input_category.value = null

}
const remove = todo => {
  todos.value = todos.value.filter(t => t !== todo)
}


watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, {deep: true})

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})
onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})


</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">whats app, <input type="text" placeholder="name here" v-model="name"/></h2>
    </section>
    <section class="todo-create">
      <h1>Create Todo</h1>

      <form @submit.prevent="addTodo">
        <h4>Whats on your todo list?</h4>
        <input 
          type="text" 
          placeholder="catat disini" 
          v-model="input_content"/>

          <h3>Pick Category:</h3>
          <div class="options">
            <label>
              <input 
                type="radio"
                name="category"
                value="bussines" 
                v-model="input_category"/>
                <span>Bussiness</span>
            </label>
            <label>
              <input 
                type="radio"
                name="category"
                value="personal" 
                v-model="input_category"/>
                <span>Personal</span>
            </label>

          </div>
          <input type="submit" value="add todo">  
      </form>
    </section>
    <section class="todo-list">
      <h3>Your list</h3>
      <div class="list">
        <div v-for="todo in todos_asc" :class="`todo-item ${todo.complete && 'complete'}`">
          
          <label>
            <input type="checkbox" v-model="todo.complete"/>
            <!-- <span></span> -->
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content">
          </div>
          <button class="delete" @click="remove(todo)">delete</button>
        </div>
      </div>
    </section>
  </main>
</template>


