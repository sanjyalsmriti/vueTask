<template>
  <div class="app">
    <header class="header">
      <h1>Shop Here</h1>
      <div class="cart-badge">
         Cart <span>0</span>
      </div>
    </header>
    <div class="container">
      <div class="card">
        <h2>Add Product</h2>
        <div class="form">
          <input v-model="newName" placeholder="Product name" />
          <input v-model.number="newPrice" type="number" placeholder="Price" />
          <button class="primary" @click="addProduct">Add Product</button>
        </div>
      </div>

      <div class="card">
        <h2>Products</h2>

        <div v-if="products.length === 0">No products yet</div>
        <div class="grid">
          <div
            class="product-card"
            v-for="product in products"
            :key="product.id"
          >
            <h3>{{ product.name }}</h3>
            <p class="price">Rs {{ product.price }}</p>

            <div class="actions">
              <button class="primary">Add to Cart</button>
              <button class="danger">Delete</button>
            </div>
          </div>
        </div>
      </div>

      <div class="card">
        <h2>Your Cart</h2>
        <p>Cart is empty</p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue"

const products = ref([])

const newName = ref("")
const newPrice = ref("")

const addProduct = () => {
  if (!newName.value || !newPrice.value) return

  products.value.push({
    id: Date.now(),
    name: newName.value,
    price: newPrice.value
  })

  newName.value = ""
  newPrice.value = ""
}
</script>

<style>
* {
  box-sizing: border-box;
  color: #ceb15f;
  font-weight: bold;
}

body {
  margin: 0;
  font-family: Arial, Helvetica, sans-serif;
  background: #f4f6f9;
}


.header {
  background: linear-gradient(135deg, #4f46e5, #7c3aed);
  color: white;
  padding: 15px 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-radius: 10px;
}

.cart-badge span {
  background: white;
  color: #4f46e5;
  padding: 3px 8px;
  border-radius: 50%;
  margin-left: 5px;
  font-weight: bold;
}

/* Layout */
.container {
  max-width: 900px;
  margin: 20px auto;
  padding: 10px;
}

/* Card */
.card {
  background: white;
  padding: 15px;
  margin-bottom: 20px;
  border-radius: 10px;
  box-shadow: 0 4px 10px rgba(0,0,0,0.05);
}

/* Form */
.form input {
  margin-right: 10px;
  padding: 8px;
}

/* Product Grid */
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 15px;
}

.product-card {
  background: #fafafa;
  padding: 12px;
  border-radius: 8px;
  text-align: center;
}

.price {
  color: #4f46e5;
  font-weight: bold;
}

/* Buttons */
button {
  padding: 6px 10px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.primary {
  background: #4f46e5;
  color: white;
}

.danger {
  background: #ef4444;
  color: white;
}
</style>