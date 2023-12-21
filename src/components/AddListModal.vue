<template>
  <!-- Overlay for the add list modal -->
  <div class="modal-overlay">
    <div class="modal-container bg-white rounded shadow-lg p-4">
      <!-- Header of the modal -->
      <div class="modal-header flex justify-between items-center mb-4">
        <h3 class="text-lg font-semibold">Add New List</h3>
        <button @click="closeModal" class="text-xl font-bold">&times;</button>
      </div>

      <!-- Form for adding a new list -->
      <form @submit.prevent="addList">
        
        <!-- Input field for list title -->
        <div class="form-group mb-4">
          <label for="listTitle" class="block text-sm font-medium text-gray-700">List Name</label>
          <input type="text" id="listTitle" v-model="newListTitle"
            class="mt-1 block w-full border border-gray-300 rounded shadow-sm focus:ring-blue-500 focus:border-blue-500" placeholder=" List Name"
            required>
        </div>

  <!-- Input fields for list items -->
  <div class="form-group mb-4">
    <label class="block text-sm font-medium text-gray-700">Items</label>
    <!-- Loop through and manage list items -->
    <div v-for="(item, index) in newList.items" :key="index" class="flex items-center mb-2">
      <input type="number" v-model="item.quantity"
        class="item-quantity-input border border-gray-300 rounded shadow-sm mr-2"
        placeholder=" Qty" min="0">
      <input type="text" v-model="item.name"
        class="item-name-input border border-gray-300 rounded shadow-sm"
        placeholder=" Item name">
      <button @click="removeItem(index)"
        class="remove-item-button bg-purple-400 hover:bg-purple-500 text-white font-bold py-1 px-2 rounded">Remove</button>
    </div>
    <!-- Button to add a new item -->
    <button @click="addItem" type="button"
      class="add-item-button bg-indigo-500 hover:bg-indigo-600 text-white font-bold py-1 px-2 rounded"> <i class="fa fa-plus"
        aria-hidden="true"></i> Add Item</button>
    <!-- Display validation error if no items are added -->
    <span v-if="validationErrors.items" class="text-red-500">{{ validationErrors.items }}</span>
  </div>

        <!-- Submit Button to save the new list -->
        <div class="form-group text-right">
          <button type="submit" class="bg-lime-500 hover:bg-lime-600 text-white font-bold py-2 px-4 rounded mt-4"> <i
              class="fa fa-floppy-o" aria-hidden="true"></i> Save
            List</button>
        </div>
      </form>
    </div>
  </div>
</template>
  
<script>
export default {
  name: 'AddListModal',
  data() {
    return {
      newListTitle: '',                // Title of the new list
      newList: {
        title: '',
        items: [],
        updatedAt: new Date().toISOString()
      },
      validationErrors: {
        items: ''
      }
    };
  },
  methods: {
    // Function to add a new list
    addList() {
      // Reset error message
      this.validationErrors.items = '';

      // Filter out empty items for validation
      const nonEmptyItems = this.newList.items.filter(item => item.name.trim());

      // Check if there's at least one non-empty item
      if (nonEmptyItems.length === 0) {
        this.validationErrors.items = 'Please add at least one item.';
        return;
      }

      const newList = {
        title: this.newListTitle,
        items: nonEmptyItems,
        updatedAt: new Date().toISOString()
      };

      // API call to add the new list
      fetch('http://localhost:3000/lists/', {
        method: 'POST',
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(newList)
      })
        .then(res => res.json())
        .then(data => {
          this.$emit('listAdded', data); // Emit event when list is added
          this.closeModal();             // Close the modal
        })
        .catch(error => console.error('Error:', error));
    },
    // Function to add a new item to the list
    addItem() {
      const newItem = {
        id: Date.now() + Math.random(), // Generate a simple unique ID
        name: '',
        quantity: "",
        purchased: false
      };
      this.newList.items.push(newItem);
    },
    // Function to remove an item from the list by index
    removeItem(index) {
      this.newList.items.splice(index, 1);
    },
    // Function to close the modal and reset the form data
    closeModal() {
      this.newListTitle = '';
      this.newList.items = [];
      this.$emit('closeModal');
    }
  }
};
</script>
  
<style>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
