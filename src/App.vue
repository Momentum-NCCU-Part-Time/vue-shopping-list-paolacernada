<template>
  <div class="app-container">
    <h1>Vue Shopping List</h1>
    <ShoppingList v-for="list in shoppingLists" :key="list.id" :list="list" />
  </div>
</template>

<script>
import ShoppingList from './components/ShoppingList.vue';

export default {
  name: 'App',
  components: {
    ShoppingList,
    ListItem
  },
  data() {
    return {
      shoppingLists: []
    };
  },
  mounted() {
    // Fetch lists from API
  }
};
</script>

<style scoped>
.app-container {
  
}
</style>
