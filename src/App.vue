<template>
  <!-- Main container for the application -->
  <div class="app-container">
    <!-- Header displaying the app's title and the add new list button -->
    <header class="app-header">
      <h1>Shopping Lists</h1>
      <!-- Button to add a new shopping list -->
      <button @click="showAddListModal = true" class="add-list-button">
        <i class="fa fa-list-alt" aria-hidden="true"></i> Add New List
      </button>
    </header>

    <!-- Shopping Lists Display -->
    <div class="shopping-lists-container">
      <!-- Loop through shoppingLists and render ShoppingList components -->
      <ShoppingList
        v-for="list in shoppingLists"
        :key="list.id"
        :list="list"
        @updateList="fetchLists"
        @deleteList="fetchLists"
      />
    </div>

    <!-- Add List Modal -->
    <AddListModal
      v-if="showAddListModal"
      @closeModal="showAddListModal = false"
      @listAdded="fetchLists"
    />
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
      shoppingLists: [],       // Array to store shopping lists
      showAddListModal: false  // Control variable for displaying AddListModal
    };
  },
  mounted() {
    // Call fetchLists when the component is mounted
    this.fetchLists();
  },
  methods: {
    // Fetch shopping lists from the server and update shoppingLists
    fetchLists() {
      // Make a GET request to retrieve shopping lists
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

<style>
.app-container {
  max-width: 1200px;
  margin: auto;
  padding: 20px;
}

.app-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}

.app-header h1 {
  font-size: 2rem;
  color: #333;
}

.add-list-button {
  background-color: #4CAF50; /* Green */
  border: none;
  color: white;
  padding: 10px 24px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  margin: 4px 2px;
  transition-duration: 0.4s;
  cursor: pointer;
  border-radius: 8px;
  box-shadow: 0 4px #999;
}

.add-list-button:hover {
  background-color: #45a049;
  box-shadow: 0 5px #666;
  transform: translateY(-2px);
}

.shopping-lists-container {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  justify-content: center;
}

/* Additional styles if needed */
</style>
