<template>
  <div class="modal-overlay">
    <div class="modal-container bg-white rounded shadow-lg p-4">
      <!-- Header -->
      <div class="modal-header flex justify-between items-center mb-4">
        <h3 class="text-lg font-semibold">Add New List</h3>
        <button @click="closeModal" class="text-xl font-bold">&times;</button>
      </div>

      <!-- Add List Form -->
      <form @submit.prevent="addList">
        <!-- List Title Input -->
        <div class="form-group mb-4">
          <label for="listTitle" class="block text-sm font-medium text-gray-700">List Title</label>
          <input type="text" id="listTitle" v-model="newListTitle"
            class="mt-1 block w-full border border-gray-300 rounded shadow-sm focus:ring-blue-500 focus:border-blue-500"
            required>
        </div>

        <!-- List Items Input -->
        <div class="form-group mb-4">
          <label class="block text-sm font-medium text-gray-700">Items</label>
          <div v-for="(item, index) in newList.items" :key="index" class="flex mb-2">
            <input type="text" v-model="item.name"
              class="mt-1 block w-full border border-gray-300 rounded shadow-sm focus:ring-blue-500 focus:border-blue-500 mr-2"
              placeholder="Item name">
            <button @click="removeItem(index)"
              class="bg-red-500 hover:bg-red-700 text-white font-bold py-1 px-2 rounded">Remove</button>
          </div>
          <button @click="addItem" type="button"
            class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-1 px-2 rounded">Add Item</button>
          <span v-if="validationErrors.items" class="text-red-500">{{ validationErrors.items }}</span>
        </div>

        <!-- Submit Button -->
        <div class="form-group text-right">
          <button type="submit" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">Add
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
    addList() {

      // Reset error message
      this.validationErrors.items = '';

      // Check if there's at least one item and every item has a non-empty name
      if (this.newList.items.length === 0 || this.newList.items.some(item => !item.name.trim())) {
        this.validationErrors.items = ' Please add at least one item.';
        return;
      }

      const newList = {
        title: this.newListTitle,
        items: this.newList.items.map(item => ({
          name: item.name,
          purchased: false
        })),
        updatedAt: new Date().toISOString()
      };

      fetch('http://localhost:3000/lists/', {
        method: 'POST',
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(newList)
      })
        .then(res => res.json())
        .then(data => {
          this.$emit('listAdded', data);
          this.closeModal();
        })
        .catch(error => console.error('Error:', error));
    },
    addItem() {
      this.newList.items.push({ name: '', purchased: false });
    },
    removeItem(index) {
      this.newList.items.splice(index, 1);
    },
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

.modal-container {}
</style>
  