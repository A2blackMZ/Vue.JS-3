<script setup>
import { ref, watch, computed } from "vue";
import { uid } from "uid";
import { Icon } from '@iconify/vue';
import TodoCreator from "../components/TodoCreator.vue";
import TodoItem from "../components/TodoItem.vue";
const todoList = ref([]);

watch(
  todoList,
  () => {
    setTodoListLocalStorage();
  },
  {
    deep: true,
  }
);

const todoCompleted = computed(() => {
  return todoList.value.every((todo) => todo.isCompleted);
});

const fechtTodoList = () => {
  const savedTodoList = JSON.parse(localStorage.getItem("todoList"));
  if (savedTodoList) {
    todoList.value = savedTodoList;
  }
};

fechtTodoList();

const setTodoListLocalStorage = () => {
  localStorage.setItem("todoList", JSON.stringify(todoList.value));
};

const createTodo = (todo) => {
  todoList.value.push({
    id: uid(),
    todo,
    isCompleted: null,
    isEditing: null,
  });
  setTodoListLocalStorage();
};

const toogleTodoComplete = (todoPos) => {
  todoList.value[todoPos].isCompleted = !todoList.value[todoPos].isCompleted;
  setTodoListLocalStorage();
};

const toogleEditTodo = (todoPos) => {
  todoList.value[todoPos].isEditing = !todoList.value[todoPos].isEditing;
  setTodoListLocalStorage();
};

const updateTodo = (todoVal, todoPos) => {
  todoList.value[todoPos].todo = todoVal;
  setTodoListLocalStorage();
};

const deleteTodo = (todoId) => {
  todoList.value = todoList.value.filter((todo) => todo.id !== todoId);
  setTodoListLocalStorage();
}
</script>

<template>
  <main>
    <h1>Create Todo</h1>
    <TodoCreator @create-todo="createTodo" />
    <ul class="todo-list" v-if="todoList.length > 0">
      <TodoItem 
        v-for="(todo, index) in todoList" 
        :todo="todo" 
        :index="index" 
        @toogle-complete="toogleTodoComplete" 
        @edit-todo="toogleEditTodo" 
        @update-todo="updateTodo" 
        @delete-todo="deleteTodo"
      />
    </ul>
    <p class="todos-msg">
      <Icon icon="noto-v1:sad-but-relieved-face" width="22" />
      <span>You have no todo's to complete! Add one!</span>
    </p>
    <p v-if="todoCompleted && todoList.length > 0" class="todos-msg">
      <Icon icon="noto-v1:party-popper" />
      <span>You have completed all your todos!</span>
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
        text-align: center;
        margin-bottom: 16px;
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
}
</style>
