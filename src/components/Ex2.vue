<script>
export default {
  data() {
    return {
      items: ['keyboard', 'mouse', 'iPhone', 'macbook', 'adapter'],
      newItem: ''
    }
  },
  methods: {
    addItem() {
      if (this.newItem.trim() !== '') {
        this.items.push(this.newItem)
        this.newItem = '' // clear input box
      }
    },
    deleteItem(index) {
      this.items.splice(index, 1)
    }
  }
}
</script>

<template>
  <div>
    <h2>Shopping Cart</h2>
    <ul>
      <li v-for="(item, index) in items" :key="index">
        {{ item }}
        <button @click="deleteItem(index)">Delete!</button>
      </li>
    </ul>

    <input v-model="newItem" placeholder="Add new item" />
    <button @click="addItem">Add!</button>
  </div>
</template>

