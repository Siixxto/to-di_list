<script setup>
import {ref, onMounted, computed, watch} from 'vue'

const arrtodo = ref([])
const arruser = ref([])
const currentuser = ref('')

const todoinput = ref('')
const todocategory = ref(null)

const sorttodo = computed(() => arrtodo.value.sort((a, b) => {
    return b.createddate - a.createddate
}))

const togglenewtodo = () => { 
  console.log('toggle')
  if (todoinput.value.trim() === '' || todocategory.value === null) {
    return
  }

  arrtodo.value.push({
    content : todoinput.value,
    category : todocategory.value,
    username: currentuser.value,
    done : false,
    createddate: new Date().getTime()
  })

  todoinput.value = ''
  todocategory.value = null
}

const removetodolist = todo => {
     arrtodo.value = arrtodo.value.filter(todos => todos !== todo)
}

watch(arrtodo, newtodo => {
  localStorage.setItem('arrtodo' , JSON.stringify(newtodo))
}, { deep: true })


watch(currentuser , (userlogin)=> {
  localStorage.setItem('currentuser' , userlogin)
})

onMounted(() => {
  currentuser.value = localStorage.getItem('currentuser')  || ''
  
})
</script>

<template>
  <main class="main-app--form">
    <div class="create-todo--form">

      <section class="create-todo--input">  

        <h3> Make your new to-do list, {{ currentuser}}</h3> 

        <form @submit.prevent="togglenewtodo"> 
          <input 
            type="text" 
            placeholder="Your to-do..."
            class="input-todo"
            v-model="todoinput"
          >
        
          <h3>Category</h3>

          <div class="category--format"> 
              <label 
                class="category--form" 
                id="low"
              >
                <input 
                  type="radio"
                  name="category" 
                  value="Office"
                  v-model="todocategory"
                /> 
                <h4 class="radio-format">Low Priority</h4>
              </label> 
 
              <label 
                class="category--form" 
                id="medium"
              >
                <input 
                  type="radio"
                  name="category"
                  value="Medium"
                  v-model="todocategory"
                /> 
                <h4 class="radio-format">Medium Priority</h4>
              </label> 
 
              <label 
                class="category--form" 
                id="high"
              >
                <input 
                  type="radio"
                  name="category"
                  value="High"
                  v-model="todocategory"
                />
                <h4 class="radio-format">High Priority</h4>
              </label> 

          </div>

          <input type="submit" value="Add To-do" class="button--form">
        </form>
      </section> 
    </div>
    {{ sorttodo }}
    <div>
      <section class="todo-list">
        <h2>TO-DO LIST</h2>
        <div>
          <div v-for="todo in sorttodo" :key="todo" :class="`to-do-item ${todo.done && 'done'}`"  >
            <label class="todo-task--form">
              <input type="checkbox" v-model='todo.done'/>
              <span></span>

              <div class="todo-content--form">
                <input type="text" v-model="todo.content"/>
              </div>

              <div class="action--form">
                <button class="delete--form" @click="removetodolist(todo)">Delete</button>
              </div>
              
            </label>
          </div>
        </div>
      </section>
    </div>
  </main>
</template>

<style scoped>
</style>
