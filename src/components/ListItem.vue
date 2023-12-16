<template>
  <div class="flex items-center justify-between bg-white p-2 rounded shadow mb-2">
    <!-- Item Name -->
    <span :class="{ 'line-through': item.purchased }">{{ item.name }}</span>

    <!-- Toggle Purchase Status -->
    <button @click="togglePurchased" class="text-sm font-bold py-1 px-2 rounded text-blue-500 hover:text-blue-700">
      {{ item.purchased ? 'Unmark' : 'Mark' }}
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
    listId: Number
  },
  methods: {
    togglePurchased() {

      fetch(`http://localhost:3000/lists/${this.listId}`, {
        method: 'PATCH',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ purchased: !this.item.purchased })
      })
        .then(response => response.json())
        .then(() => {
          this.item.purchased = !this.item.purchased;
          this.$emit('ItemUpdated');
        })
        .catch(error => console.error('Error:', error));
    }
  }
};
</script>
  
<style></style>
  