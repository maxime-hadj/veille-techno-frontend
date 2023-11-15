<script setup>
import{ uid } from 'uid';
import { Icon } from '@iconify/vue';
import { ref } from 'vue';
import TodoCreator from '../components/TodoCreator.vue';
import TodoItem from '../components/TodoItem.vue';
const todoList = ref([]);

const createTodo = (todo) =>{
  todoList.value.push({
    id: uid(),
    todo,
    isCompleted: null,
    isEditing: null,
  });
}

const toggleTodoComplete = (todoPos) => {
  todoList.value[todoPos].isCompleted = !todoList.value[todoPos].isCompleted
};

const toggleEditTodo =(todoPos) => {
  todoList.value[todoPos].isEditing = !todoList.value[todoPos].isEditing
};

const updateTodo= (todoVal, todoPos) => {
  todoList.value[todoPos].todo = todoVal
};

const deleteTodo = (todoId) => {
  todoList.value = todoList.value.filter((todo) => todo.id !== todoId)
};
</script>

<template>
  <main>
    <h1>Créer une liste de tâches diaboliques.</h1>
    <TodoCreator @create-todo="createTodo" />
    <ul class="todo-list" v-if="todoList.length > 0">
      <TodoItem v-for="(todo , index) in todoList" :todo="todo" :index="index"  @toggle-complete="toggleTodoComplete" @edit-todo="toggleEditTodo" @update-todo="updateTodo" @delete-todo="deleteTodo"/>
    </ul>
    <p class="todo-msg" v-else>
      <Icon icon="game-icons:evil-book" color="red" />
      <span>Votre liste de méfaits est vide !!! Les forces du bien vont reprendre le dessus.</span>
    </p>
  </main>
</template>


<style scoped lang="scss">
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
}
</style>