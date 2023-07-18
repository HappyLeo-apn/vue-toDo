<script setup>
import ToDoCreator from "../components/ToDoCreator.vue";
import ToDoItem from '../components/ToDoItem.vue';
import { uid } from 'uid';
import { ref,watch,computed } from "vue";
import { Icon } from '@iconify/vue';
const todoList = ref([]);

watch(
  todoList,
  () => {
    setToDoListLocalStorage();
  },
  {
    deep : true
  }
);

const toDoCompleted = computed(() => {
  return todoList.value.every((todo) => todo.isCompleted);
});

const fetchToDoList = () => {
  const savedToDoList = JSON.parse(localStorage.getItem('todoList'))
  if(savedToDoList){
    todoList.value = savedToDoList
  }
}
fetchToDoList();

const setToDoListLocalStorage = () => {
  localStorage.setItem("todoList", JSON.stringify(todoList.value));
}

const createToDo = (todo) => {
  todoList.value.push({
    id : uid(),
    todo,
    isCompleted : null,
    isEditing : null
  })
  
}
const toggleTodoComplete = (todoPos) => {
  todoList.value[todoPos].isCompleted = ! todoList.value[todoPos].isCompleted ;
  
}

const toggleEditTodo = (todoPos) => {
  todoList.value[todoPos].isEditing = ! todoList.value[todoPos].isEditing;
  
}

const toDoUpdate = (toDoVal, toDoPos) => {
  todoList.value[toDoPos].todo = toDoVal;
  
}

const deleteToDo = (todo) => {
  todoList.value = todoList.value.filter((todoFilter) =>  todoFilter.id !== todo.id); 
  
}


  </script>

<template>
  <main>
    <h1>Create To DO</h1>
   <ToDoCreator @create-todo="createToDo"></ToDoCreator>
   <ul class="todo-list" v-if="todoList.length > 0">
    <ToDoItem v-for="(todo,index) in todoList" :todo="todo" :index="index" @edit-todo="toggleEditTodo" @update-todo="toDoUpdate"  @toggle-complete="toggleTodoComplete" @delete-todo ="deleteToDo"></ToDoItem>
   </ul>
   <p class="todos-msg" v-else>
    <Icon icon="noto-v1:sad-but-relieved-face"  width="22" />
    <span>You have no todo's to complete. Add one.</span>
   </p>
   <p class="todos-msg" v-if="toDoCompleted && todoList.length > 0">
    <Icon icon="noto-v1:party-popper"  width="22" />
    <span>You have completed all your todos.</span>
   </p>
  </main>
</template>

<style lang="scss" scoped>
main {
  display: flex;
  flex-direction: column;
  max-width: 500px;
  width: 100%;
  margin: 0 auto;
  padding: 40px 16px;

  h1 {
    margin-bottom: 16px;
    text-align: center;
  }

  .todo-list {
    display: flex;
    flex-direction: column;
    list-style: none;
    margin-top: 24px;
    gap: 20px;
  }

  .todos-msg {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    margin-top: 24px;
  }
}</style>
