<template>
    <div class="list-container">
      <h2>{{ list.title }}</h2>
      <ListItem v-for="item in list.items" :key="item.id" :item="item" />
    </div>
  </template>
  
  <script>
  import ListItem from './ListItem.vue';
  
  export default {
    name: 'ShoppingList',
    props: ['list'],
    components: {
      ListItem
    }
  };
  </script>
  
  <style>
  .list-container {
    
  }
  </style>
  