<template>
  <div @keydown.enter="saveToDo" class="home">
    <div class="container d-flex">
      <h2>{{message}}</h2>
      <div v-show="deleting">
        <h2 class="error">{{deleting}}</h2> <button class="undo" @click="undo"> Undo </button>
        <div class="total-progress"></div>
      </div>
      <div>
        <input  v-model="todoText" type="text" />
          <button @click="saveToDo" >Save</button>
      </div>
      <ul>
       <li v-for="todo in todos" :key="todo">
         <span @dblclick="updateTodo(todo)">{{todo}} </span>
         <span class="delete" @click="deleteTodo(todo)">delete</span>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>

export default {
  name: "Home",
  data() {
    return {
       todoText: '',
       deleting:'',
       todos:[2,3,4,5],
       message:'Merhaba hoş geldin',
       deletingTimeOut:null
    }
  },
  methods:{
    saveToDo(){
      if(this.todoText){
        const text = this.todoText;
        this.todos.push(text);
        this.todoText = '';
      } else {
        alert('Lütfen bir şey yazınız');  
      }
    },
    updateTodo(todo){
      const updatedText = prompt("Please enter updated todo", todo);
      const index =  this.todos.indexOf(todo);
      this.todos.splice(index,1,updatedText);
      
    },
    undo(){
      clearTimeout(this.deletingTimeOut);
      this.deleting = ' delete cancelled';
      setTimeout(()=> this.deleting = '',2500);
    },
    deleteTodo(todo){
      this.deleting  = `${todo} is deleting right now.`
      this.deletingTimeOut = setTimeout(() => {
        this.todos = this.todos.filter(x => x!== todo);
        this.deleting = '';
      },5000);
      /* const index = this.todos.indexOf(todo);
       this.todos.splice(index,1);
      */
      /* 
      const newTodos = [];
       this.todos.forEach(element => {
         if(element !== todo){
           newTodos.push(element);
         }
       }); 
       */
    }
  }
};
</script>

<style scoped>
h2 {
  color: black;
}
.error {
  color: red;
  font-size: bold;
}
.undo {
  display: block;
  width:100%;
  margin-top:10px;
  margin-bottom: 10px;
  background: tomato;
  border: none;
  color: white;
  border-radius: 5px;
  font-size: 20px;
  cursor: pointer;
  margin:0;
}
.undo:hover {
  background: rgb(248, 121, 98);
}
.total-progress {
  width: 100%;
  background: gray;
  height: 10px;
  margin-bottom: 5px;
  margin-top: 5px;
}

</style>