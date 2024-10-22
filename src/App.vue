<script setup>
  import {ref, computed, watch, onMounted} from 'vue'

  const todos = ref([])
  const name = ref('')

  const input_text = ref('')
  const input_radio = ref(null)

  const todos_asc = computed(() => todos.value.sort((a, b) => {
    return a.createdAt - b.createdAt
  }))

  watch(todos, newVal => {
    localStorage.setItem('todos', JSON.stringify(newVal))
  }, { deep: true })

  watch(name, (newVal) => {
    localStorage.setItem('name', newVal)
  })

  const addTodo = () => {
    if(input_text.value.trim() === '' || input_radio.value === null){
      return
    }
    todos.value.push({
      content: input_text.value,
      category: input_radio.value,
      done: false,
      editable: false,
      createAt: new Date().getTime()
      })
  }

  const removeTodo = todo => {
    todos.value = todos.value.filter((t) => t  !== todo)
  }

  onMounted(() => {
    name.value = localStorage.getItem('name') || ''
    todos.value = JSON.parse(localStorage.getItem('todos')) || []
  })
</script>

<template> 
  <main class="app">

    <section class="greeting">
      <h2 class="title">
        Привет, <input type="text" placeholder="Введите имя" v-model="name">
      </h2>
    </section>

    <section class="create-todo">
        <h3>CОЗДАТЬ ЗАДАЧУ</h3>
        <form id="new-todo-form" @submit.prevent = "addTodo()">
          <h4>Какие задачи на сегодня?</h4>
          <input type="text" placeholder="Например, сделать сайт" v-model="input_text">

          <h4>Выберите категорию</h4>

          <div class="options">
              <label>
              <input type="radio" name="category" v-model="input_radio">
              <span class="bubble personal"></span>
              <div>Личное</div>
              </label>

              <label>
              <input type="radio" name="category" v-model="input_radio">
              <span class="bubble buisnes"></span>
              <div>Рабочее</div>
              </label>
          </div>
          <input type="submit" value="Добавить">
        </form>   
    </section>

    <section class="todo-list">
      <h3>ЛИСТ ЗАДАЧ</h3>
      <div class="list" id="todo-list">
        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
          <label>
            <input type="checkbox" v-model="todo.done">
            <span :class="`bubble ${todo.category == 'business' ? 'business' : 'personal'}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content">
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Удалить</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

