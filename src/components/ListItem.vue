<template>
    <div class="item-container">
      {{ item.name }}
    </div>
  </template>
  
  <script>
  export default {
    name: 'ListItem',
    props: ['item']
  };
  </script>
  
  <style>
  .item-container {
    
  }
  </style>
  