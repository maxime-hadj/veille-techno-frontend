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
  todoList: {
    type: Array,
    required: true,
  },
  minTextLength: {
    type: Number,
    default: 0,
  },
  maxTextLength: {
    type: Number,
    default: 50,
  },
});

defineEmits(["toggle-complete", "edit-todo", "update-todo", "delete-todo"]);

const addingSubtask = ref(false);
const newSubtaskName = ref('');

const toggleAddingSubtask = () => {
  addingSubtask.value = !addingSubtask.value;
  newSubtaskName.value = '';
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
      description: '', // Add description property to subtask
      subtasks: []
    });
    toggleAddingSubtask();
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

// Handling subtask descriptions
const addingDescriptionIndex = ref(-1);
const newSubtaskDescription = ref('');

const toggleAddingDescription = (subtaskIndex) => {
  if (addingDescriptionIndex.value === subtaskIndex) {
    addingDescriptionIndex.value = -1;
  } else {
    addingDescriptionIndex.value = subtaskIndex;
    newSubtaskDescription.value = '';
  }
};

const addSubtaskDescription = () => {
  const currentTodoList = props.todoList;
  const currentTodo = currentTodoList[props.index];
  const subtask = currentTodo.subtasks[addingDescriptionIndex.value];

  if (subtask && newSubtaskDescription.value.trim() !== '') {
    subtask.description = newSubtaskDescription.value;
    addingDescriptionIndex.value = -1;
  }
};

</script>

<template>
  <li>
    <input type="checkbox" :checked="todo.isCompleted" @input="$emit('toggle-complete', index)" />
    <div class="todo">
      <input v-if="todo.isEditing" type="text" :minlength="5" :maxlength="15" :value="todo.todo" @input="$emit('update-todo', $event.target.value, index)">
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
        <input type="text" :minlength="5" :maxlength="15" @keyup.enter="addSubtaskWithName" v-model="newSubtaskName" placeholder="" />
        <button @click="addSubtaskWithName">Add</button>
      </div>
    </div>
  </li>

  <div v-if="todo.subtasks && todo.subtasks.length > 0">
    <ul>
      <li v-for="(subtask, subIndex) in todo.subtasks" :key="subIndex">
        <div class="subtask">
          <input type="checkbox" :checked="subtask.isCompleted" @input="toggleSubtaskComplete(subIndex)" />
          <span :class="{ 'completed-subtask': subtask.isCompleted }">
            {{ subtask.todo }}
          </span>
          <!-- Add icon for adding description to subtasks -->
          <Icon icon="mdi:text-box-plus-outline" color="red" class="icon" @click="toggleAddingDescription(subIndex)" />
          <div v-if="addingDescriptionIndex === subIndex">
            <!-- Input field for adding description -->
            <input type="text" :minlength="5" :maxlength="100" @keyup.enter="addSubtaskDescription" v-model="newSubtaskDescription" placeholder="Add description" />
            <button @click="addSubtaskDescription">Add Description</button>
          </div>
          <Icon icon="game-icons:evil-tree" color="red" class="icon" @click="deleteSubtask(subIndex)" />
        </div>
        <div v-if="subtask.description">{{ subtask.description }}</div>
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