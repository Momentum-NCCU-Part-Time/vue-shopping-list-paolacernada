<template>
  <div class="list-container my-4 p-4 border rounded shadow">
    <h2 class="font-semibold text-xl">{{ list.title }}</h2>
    <p class="text-sm text-gray-600">Last Updated: {{ formattedDate }}</p>
    <div>
      <ListItem v-for="item in list.items" :key="item.id" :item="item" :listId="list.id"
        @ItemUpdated="handleItemUpdated" />
    </div>
  </div>
</template>
  
<script>
import ListItem from './ListItem.vue';
import dayjs from 'dayjs';

export default {
  name: 'ShoppingList',
  props: {
    list: Object
  },
  components: {
    ListItem
  },
  computed: {
    formattedDate() {
      return this.list.updatedAt ? dayjs(this.list.updatedAt).format('MMMM D, YYYY h:mm A') : 'Not updated yet';
    }
  },
  methods: {
    updateListTime() {
      const updatedTime = new Date().toISOString();
      this.list.updatedAt = updatedTime;

      // API call to update the list's updatedAt time
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
    }
  },
    handleItemUpdated(itemId) {
      // Find and toggle the purchased status of the item
      const item = this.list.items.find(i => i.id === itemId);
      if (item) {
        item.purchased = !item.purchased;
      }
    }
};
</script>
  
<style>
.list-container {
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}
</style>
  