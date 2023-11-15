<script setup>

import { Icon } from '@iconify/vue';

const props = defineProps({
    todo:{
        type: Object,
        required: true,
    }
});
</script>


<template>
<li>
    <input type="checkbox" :checked="todo.isCompleted"/>
    <div class="todo">
        <input v-if="todo.isEditing" type="text" :value="todo.todo">
        <span v-else>
            {{ todo.todo }}
        </span>
    </div>
    <div class="todo-actions">
        <Icon v-if="todo.isEditing" icon="icomoon-free:evil" color="red" class="icon"/>
        <Icon v-else icon="game-icons:evil-hand" color="red" class="icon"/>
        <Icon icon="game-icons:evil-tree" color="red" class="icon"/>
    </div>
</li>
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