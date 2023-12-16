<template>
  <div class="container mx-auto p-4">
    <header class="text-center my-4">
      <h1 class="text-3xl font-bold text-blue-500">Shopping Lists</h1>
    </header>

    <!-- Shopping Lists Display -->
    <div>
      <div v-for="list in shoppingLists" :key="list.id" class="mb-6">
        <ShoppingList :list="list" @updateList="fetchLists" @deleteList="fetchLists" />
      </div>

      <!-- Add New List Button -->
      <button @click="showAddListModal = true"
        class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">
        Add New List
      </button>
    </div>

    <!-- Add List Modal -->
    <AddListModal v-if="showAddListModal" @closeModal="showAddListModal = false" @listAdded="fetchLists" />
  </div>
</template>

<script>
import ShoppingList from './components/ShoppingList.vue';
import AddListModal from './components/AddListModal.vue';

export default {
  name: 'App',
  components: {
    ShoppingList,
    AddListModal
  },
  data() {
    return {
      shoppingLists: [],
      showAddListModal: false
    };
  },
  mounted() {
    this.fetchLists();
  },
  methods: {
    fetchLists() {
      // Fetch lists from API
      fetch('http://localhost:3000/lists/')
        .then(response => response.json())
        .then(data => {
          this.shoppingLists = data;
        })
        .catch(error => console.error('Error:', error));
    }
  }
};
</script>

<style></style>
