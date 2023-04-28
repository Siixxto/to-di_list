<template>
  <main class="main-app--form">
    <div class="create-todo--form">

      <section class="create-todo--input"> 
        <h3>Good Day,</h3> 
        <select 
          v-model="currentuser"
          class="select-user--form"
          v-show="!blnadduser"
        >
          <template  v-for="user in arruser">
            <option :value="user.username">{{ user.username }}</option>
          </template> 
        </select>
        
        <input type="text" v-model="newuser" v-show="blnadduser"> 
        <button class="delete--form" @click="addnewuser()">{{blnadduser ? 'ADD' : 'ADD USER'}}</button> 
      </section>

      <section class="create-todo--input">
        <h3>Make your new to-do list, {{ currentuser }}</h3>

        <form @submit.prevent="togglenewtodo">
          <input
            type="text"
            placeholder="Your to-do..."
            class="input-todo"
            v-model="todoinput"
          />

          <h3>Category</h3>

          <div class="category--format">
            <label class="category--form" id="low">
              <input type="radio" name="category" value="Low" v-model="todocategory" />
              <h4 class="radio-format">Low Priority</h4>
            </label>

            <label class="category--form" id="medium">
              <input type="radio" name="category" value="Medium" v-model="todocategory" />
              <h4 class="radio-format">Medium Priority</h4>
            </label>

            <label class="category--form" id="high">
              <input type="radio" name="category" value="High" v-model="todocategory" />
              <h4 class="radio-format">High Priority</h4>
            </label>
          </div>

          <input type="submit" value="Add To-do" class="button--form" />
        </form>
      </section>
    </div>
    
    <div>
      <section class="todo-list">
        <h2>YOUR TO-DO LIST</h2>
        <div>
          <div
            v-for="(todo , index) in sorttodo"
            :key="index"
            class="to-do-item"
          >
            <label class="todo-task--form" > 
              <input type="checkbox" v-model="todo.done" @change="changetasktodone()">
              <span>{{ todo.username }}</span>  
              <div class="todo-content--form"> 
                <input type="text" :readonly="!todo.isreadonly" v-model="todo.content" id="taskcontent"/>
              </div>
              <span>{{ todo.category }}</span>
              <div class="action--form">
                <button class="task-button--form" id="edit" @click="edittodolist(todo)">{{todo.isreadonly ? 'Save' : 'Edit'}}</button>
                <button class="task-button--form" id="delete" @click="removetodolist(todo)">Delete</button>
              </div>
            </label>
          </div>
        </div>
      </section>
    </div>
  </main> 
</template>

<script> 
export default {
  data() {
    return {
      arrtodo : [],
      arruser : [],
      currentuser : '',
      newuser: '', 
      todoinput : '',
      todocategory : null,
      isreadonly : false,
      blnadduser : false,

      

    }
  }, 

  mounted() { 
    this.currentuser = localStorage.getItem('currentuser')  || ''
    this.arrtodo = JSON.parse(localStorage.getItem('arrtodo')) || []
    this.arruser = JSON.parse(localStorage.getItem('arruser')) || [] 

  },

  watch: {
    arrtodo(newtodo){
      localStorage.setItem('arrtodo' , JSON.stringify(newtodo))
      ,{ deep: true }
    },

    arruser(users){ 
      localStorage.setItem('arruser', JSON.stringify(users))
      ,{ deep: true }
    },

    currentuser(user) {
       localStorage.setItem('currentuser', user)
    },
  },

  computed:{
    sorttodo(){
      return this.arrtodo.filter(data => {
        return data.username === this.currentuser
      }).sort((a, b) => {
        return b.createddate - a.createddate
      })
    }
  },

  methods: {
    togglenewtodo(){
      if (this.todoinput.trim() === '' || this.todocategory === null) {
        return
      }

      this.arrtodo.push({  
        username: this.currentuser,
        content : this.todoinput,
        category : this.todocategory, 
        done : false,
        createddate: new Date().getTime(),
        isreadonly : false
      })
      localStorage.setItem('arrtodo', JSON.stringify(this.arrtodo))
      this.todoinput = ''
      this.todocategory = null
    },

    edittodolist(todo) {
      this.arrtodo.filter(arr=> arr.createddate === todo.createddate).map(task=>{
                  task.isreadonly = !todo.isreadonly
                return task
      })

      localStorage.setItem('arrtodo', JSON.stringify(this.arrtodo))
    },

    removetodolist(todo){
      this.arrtodo = this.arrtodo.filter(todos => todos !== todo)
    },

    addnewuser() { 
      if (this.blnadduser) {
        if (this.newuser.trim() !== '') { 
          this.arruser.push({
            userid: this.arruser.length + 1,
            username : this.newuser
          })
          localStorage.setItem('arruser', JSON.stringify(this.arruser))
        }
      }
      this.newuser = ''
      this.blnadduser = !this.blnadduser  
    },

    changetasktodone(){ 
      localStorage.setItem('arrtodo', JSON.stringify(this.arrtodo))
    },
    
  },
  
};

</script>

<style scoped></style>
