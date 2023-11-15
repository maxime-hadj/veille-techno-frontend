<script setup>
import { RouterView } from 'vue-router'
import { Icon } from '@iconify/vue';
import ToDoHeader from './components/TodoHeader.vue';


</script>

<template>
  <ToDoHeader/>
  <RouterView />
</template>

<style lang="scss">
@import url('https://fonts.googleapis.com/css?family=Oswald');

* {
  font-family: 'Oswald';
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.container {
  max-width: 1100px;
  margin: 0 auto;
}

</style>
