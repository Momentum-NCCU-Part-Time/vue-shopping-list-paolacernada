<template>
  <!-- Display for an individual shopping list item -->
  <div class="flex items-center justify-between bg-white p-2 rounded shadow mb-2">
    <!-- Item Name with optional quantity -->
    <span :class="{ 'line-through': item.purchased }">
      <span v-if="quantity">{{ quantity }} - </span>{{ item.name }}    
    </span>

    <!-- Button to toggle purchase status -->
    <button @click="togglePurchased" class="text-sm font-bold py-1 px-2 rounded text-indigo-500 hover:text-indigo-700">
      {{ item.purchased ? 'Uncheck' : 'Check' }}
    </button>
  </div>
</template>
  
<script>
export default {
  name: 'ListItem',
  props: {
    item: {
      type: Object,
      required: true
    },
    quantity: Number, 
    listId: Number     
  },
  methods: {
    // Toggle the purchased status of the item
    togglePurchased() {
      // Make an API call to update the purchased status of the item
      fetch(`http://localhost:3000/lists/${this.listId}`, {
        method: 'PATCH',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ purchased: !this.item.purchased })
      })
        .then(response => response.json())
        .then(() => {
          this.$emit('ItemUpdated', this.item.id);
        })
        .catch(error => console.error('Error:', error));
    }
  }
};
</script>
  
<style></style>