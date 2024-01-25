<template>
  <!-- Container for shopping list display -->
  <div class="list-container my-2 p-3 border rounded shadow">
    <div v-if="isEditMode">
      <!-- Edit mode for list title and items -->
      <input v-model="editableList.title" class="title-input" />

      <!-- Editable fields for items -->
      <!-- Loop through and manage list items -->
      <div v-for="(item, index) in editableList.items" :key="index" class="flex items-center mb-2">
        <input type="number" v-model="editableList.items[index].quantity" min="0" class="quantity-input mr-2"
          placeholder="Quantity" />
        <input v-model="editableList.items[index].name" class="item-input" placeholder="Item name" />
        <button @click="removeItem(index)"
          class="remove-item-button bg-purple-400 hover:bg-purple-500 text-white font-bold py-1 px-2 rounded ml-2">
          <i class="fa fa-minus-square-o" aria-hidden="true"></i>
        </button>
      </div>

      <!-- Buttons for editing and saving changes -->
      <button @click="addItem" class="add-item-button bg-indigo-500 hover:bg-indigo-600 text-white font-bold py-1 px-2 rounded mr-3">
        <i class="fa fa-plus" aria-hidden="true"></i> Add Item
      </button>

      <button @click="saveEdits" class="save-button bg-lime-500 hover:bg-lime-600 text-white font-bold py-1 px-2 rounded mr-3">
        <i class="fa fa-floppy-o" aria-hidden="true"></i> Save
      </button>

      <button @click="cancelEdits" class="cancel-button bg-gray-500 hover:bg-gray-600 text-white font-bold py-1 px-2 rounded mr-3">
        <i class="fa fa-ban" aria-hidden="true"></i> Cancel
      </button>
    </div>

    <div v-else>
      <!-- Display mode for list title and items -->
      <h2 class="font-semibold text-xl">{{ list.title }}</h2>
      <p class="text-sm text-gray-600">Last Updated: {{ formattedDate }}</p>
      <ul class="list-disc list-inside">
        <!-- Loop through and display list items -->
        <ListItem v-for="(item, index) in list.items" :key="item.id" :item="item" :quantity="item.quantity"
          :listId="list.id" @ItemUpdated="handleItemUpdated" />
      </ul>

      <!-- Buttons for editing and deleting the list -->
      <button @click="enterEditMode"
        class="edit-list-button bg-orange-300 hover:bg-orange-400 text-white font-bold py-1 px-2 rounded mr-3 mt-1">
        <i class="fa fa-pencil" aria-hidden="true"></i> Edit List
      </button>

      <button @click="deleteList"
        class="delete-list-button bg-gray-500 hover:bg-gray-600 text-white font-bold py-1 px-2 rounded focus:outline-none mt-1">
        <i class="fa fa-trash-o" aria-hidden="true"></i> Delete List
      </button>
    </div>
  </div>
</template>
  
<script>
import ListItem from './ListItem.vue';
import dayjs from 'dayjs';

export default {
  name: 'ShoppingList',
  props: {
    list: Object  // The shopping list object as a prop
  },
  components: {
    ListItem
  },
  computed: {
    // Format the last updated date for display
    formattedDate() {
      return this.list.updatedAt ? dayjs(this.list.updatedAt).format('MMMM D, YYYY h:mm A') : 'Not updated yet';
    }
  },
  data() {
    return {
      isEditMode: false,   // Toggle for edit mode
      editableList: null,  // Temporary editable list
    };
  },
  methods: {
    // Update the last updated time for the list
    updateListTime() {
      // Update the updatedAt property with the current time
      const updatedTime = new Date().toISOString();
      this.list.updatedAt = updatedTime;

      // Make an API call to update the list's updatedAt time on the server
      fetch(`http://localhost:3000/lists/${this.list.id}`, {
        method: 'PATCH',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ updatedAt: updatedTime })
      })
        .then(response => response.json())
        .then(updatedList => {
          this.$emit('listUpdated', updatedList);
        })
        .catch(error => console.error('Error:', error));
    },
    // Delete the current list
    deleteList() {
      // Make an API call to delete the list from the server
      fetch(`http://localhost:3000/lists/${this.list.id}`, {
        method: 'DELETE'
      })
        .then(() => {
          this.$emit('deleteList');
        })
        .catch(error => console.error('Error:', error));
    },
    // Toggle the purchased state of an item
    togglePurchased(item) {
      item.purchased = !item.purchased;
    },
    // Handle updates to individual list items
    handleItemUpdated(itemId) {
      const itemIndex = this.list.items.findIndex(i => i.id === itemId);
      if (itemIndex !== -1) {
        this.list.items[itemIndex] = {
          ...this.list.items[itemIndex],
          purchased: !this.list.items[itemIndex].purchased
        };
      }
    },
    // Enter edit mode for the list
    enterEditMode() {
      this.isEditMode = true;
      this.editableList = JSON.parse(JSON.stringify(this.list));
    },
    // Save edits to the list
    saveEdits() {
      // Call updateListTime to update the updatedAt property
      this.updateListTime();

      // Make an API call to save the edited list on the server
      fetch(`http://localhost:3000/lists/${this.list.id}`, {
        method: 'PATCH',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ title: this.editableList.title, items: this.editableList.items })
      })
        .then(response => response.json())
        .then(updatedList => {
          this.isEditMode = false;
          Object.assign(this.list, updatedList);
          this.$emit('listUpdated', this.list);
        })
        .catch(error => console.error('Error:', error));
    },
    // Cancel the edit mode
    cancelEdits() {
      this.isEditMode = false;
    },
    // Add a new item to the editable list
    addItem() {
      this.editableList.items.push({ id: Date.now(), name: '', quantity: '', purchased: false });
      this.editableList = Object.assign({}, this.editableList);
    },
    // Remove an item from the editable list by index
    removeItem(index) {
      this.editableList.items.splice(index, 1);
    },
  },
};
</script>
  
<style scoped>
.list-container {
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  max-width: 50%;
  margin: auto;
}

.title-input {
  border: 1px solid #ddd;
  border-radius: 4px;
  padding: 8px;
  margin-bottom: 14px;
  width: 100%;
}

.item-input,
.quantity-input {
  border: 1px solid #ddd;
  border-radius: 4px;
  padding: 8px;
  margin-bottom: 8px;
  width: calc(100% - 2rem);
}

.remove-item-button,
.add-item-button,
.save-button,
.cancel-button,
.edit-list-button,
.delete-list-button {
  border: none;
  border-radius: 4px;
  padding: 6px 12px;
  margin-right: 8px;
  cursor: pointer;
  transition: background-color 0.2s ease-in-out;
}

.remove-item-button:hover {
  background-color: #805ad5;
}

.add-item-button:hover {
  background-color: #3949ab;
}

.save-button:hover {
  background-color: #4ccb32;
  
}

.cancel-button:hover {
  background-color: #718096;
}

.edit-list-button:hover {
  background-color: #dd6b20;
}

.delete-list-button:hover {
  background-color: #718096;
}

@media (max-width: 768px) {
  .list-container {
    width: 100%;
    max-width: none;
  }
  .item-input,
  .quantity-input {
    width: 100%;
  }
}
</style>