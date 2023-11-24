<script setup>
import { Icon } from '@iconify/vue';
import { uid } from 'uid';
import { ref } from 'vue';

const props = defineProps({
  todo: {
    type: Object,
    required: true,
  },
  index: {
    type: Number,
    required: true,
  },
  todoList: { // Define todoList as a prop
    type: Array,
    required: true,
  },
});
defineEmits(["toggle-complete", "edit-todo", "update-todo", "delete-todo"]);

const addingSubtask = ref(false);
const newSubtaskName = ref('');

const toggleAddingSubtask = () => {
  addingSubtask.value = !addingSubtask.value;
  newSubtaskName.value = ''; // Reset the input field when toggling
};

const addSubtaskWithName = () => {
  if (newSubtaskName.value.trim() !== '') {
    const currentIndex = props.index;
    const currentTodoList = props.todoList;
    currentTodoList[currentIndex].subtasks.push({
      id: uid(),
      todo: newSubtaskName.value,
      isCompleted: false,
      isEditing: false,
      subtasks: []
    });
    toggleAddingSubtask(); // Hide the input field after adding the subtask
  }
};

const toggleSubtaskComplete = (subtaskIndex) => {
  const currentTodoList = props.todoList;
  const currentTodo = currentTodoList[props.index];
  const subtask = currentTodo.subtasks[subtaskIndex];
  subtask.isCompleted = !subtask.isCompleted;
};


const toggleSubtaskEdit = (subtaskIndex) => {
  const currentTodoList = props.todoList;
  const currentTodo = currentTodoList[props.index];
  const subtask = currentTodo.subtasks[subtaskIndex];
  subtask.isEditing = !subtask.isEditing;
};


const updateSubtask = (newTodo, subtaskIndex) => {
  const currentTodoList = props.todoList;
  const currentTodo = currentTodoList[props.index];
  const subtask = currentTodo.subtasks[subtaskIndex];
  subtask.todo = newTodo;
};


const deleteSubtask = (subtaskIndex) => {
  const currentTodoList = props.todoList;
  const currentTodo = currentTodoList[props.index];
  currentTodo.subtasks.splice(subtaskIndex, 1);
};
</script>

<template>
  <li>
    <input type="checkbox" :checked="todo.isCompleted" @input="$emit('toggle-complete', index)" />
    <div class="todo">
      <input v-if="todo.isEditing" type="text" :value="todo.todo"
        @input="$emit('update-todo', $event.target.value, index)">
      <span v-else :class="{ 'completed-todo': todo.isCompleted }">
        {{ todo.todo }}
      </span>
    </div>
    <div class="todo-actions">
      <Icon icon="mdi:add-bold" color="red" class="icon" @click="toggleAddingSubtask" />
      <Icon v-if="todo.isEditing" icon="icomoon-free:evil" color="red" class="icon" @click="$emit('edit-todo', index)" />
      <Icon v-else icon="game-icons:evil-hand" color="red" class="icon" @click="$emit('edit-todo', index)" />
      <Icon icon="game-icons:evil-tree" color="red" class="icon" @click="$emit('delete-todo', todo.id)" />
      <div v-if="addingSubtask">
        <input type="text" v-model="newSubtaskName" placeholder="" />
        <button @click="addSubtaskWithName">Ajouter</button>
      </div>
    </div>
  </li>

  <div v-if="todo.subtasks && todo.subtasks.length > 0">
    <ul>
      <li v-for="(subtask, subIndex) in todo.subtasks" :key="subIndex">
        <TodoItem
        v-for="(todo, index) in todoList"
        :key="todo.id"
        :todo="subtask"
        :index="subIndex"
        :todo-list="todoList" 
        @toggle-complete="toggleSubtaskComplete(subIndex)"
        @edit-todo="toggleSubtaskEdit(subIndex)"
        @update-todo="updateSubtask($event, subIndex)"
        @delete-todo="deleteSubtask(subIndex)"
        />   
      </li>
    </ul>
  </div>
</template>


<style lang="scss" scoped>

li {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 16px 10px;
  background-color: #f1f1f1;
  box-shadow: 0 20px 25px -5px rgb(0 0 0 / 0.1),
    0 8px 10px -6px rgb(0 0 0 / 0.1);

  &:hover {
    .todo-actions {
      opacity: 1;
    }
  }

  input[type="checkbox"] {
    appearance: none;
    width: 20px;
    height: 20px;
    background-color: #fff;
    border-radius: 50%;
    box-shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1), 0 2px 4px -2px rgb(0 0 0 / 0.1);

    &:checked {
      background-color: #41b080;
    }
  }

  .todo {
    flex: 1;

    .completed-todo {
        text-decoration: line-through;
    }

    input[type="text"] {
      width: 100%;
      padding: 2px 6px;
      border: 2px solid #41b080;
    }
  }

  .icon{
    margin-left: 10px;
  }

  .todo-actions {
    display: flex;
    gap: 6px;
    opacity: 0;
    transition: 150ms ease-in-out;
    .icon {
      cursor: pointer;
    }
  }
}

</style>