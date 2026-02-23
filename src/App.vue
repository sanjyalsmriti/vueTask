<template>
  <div class="container">
    <h1>Mini E-Commerce</h1>

    <div class="card">
      <h2>Add Product</h2>

      <input v-model="name" placeholder="Product Name" />

      <input v-model.number="price" type="number" placeholder="Price" />

      <button @click="addProduct">Add</button>
    </div>

    <div class="card">
      <h2>Products</h2>

      <input v-model="search" placeholder="Search product..." />

      <div
        v-for="(product, index) in filteredProducts"
        :key="product.id"
        class="product"
      >
        <span>{{ product.name }} - Rs {{ product.price }}</span>

        <div>
          <button @click="addToCart(product)">Add to Cart</button>

          <button class="delete" @click="deleteProduct(index)">
            Delete
          </button>
        </div>
      </div>
    </div>

    <div class="card">
      <h2>Cart 🛒 ({{ cart.length }})</h2>

      <div v-if="cart.length === 0">Cart is empty</div>

      <div
        v-for="(item, index) in cart"
        :key="index"
        class="product"
      >
        <span>{{ item.name }} - Rs {{ item.price }}</span>

        <button class="delete" @click="removeFromCart(index)">
          Remove
        </button>
      </div>

      <button
        v-if="cart.length"
        class="checkout"
        @click="checkout"
      >
        Checkout
      </button>
    </div>
  </div>
</template>

<script setup>

import { ref, computed, onMounted } from "vue"

const name = ref("")      
const price = ref("")     
const search = ref("")    

const products = ref([])  
const cart = ref([])      

onMounted(() => {
  products.value =
    JSON.parse(localStorage.getItem("products")) || []

  cart.value =
    JSON.parse(localStorage.getItem("cart")) || []
})

const saveProducts = () => {
  localStorage.setItem(
    "products",
    JSON.stringify(products.value)
  )
}


const saveCart = () => {
  localStorage.setItem(
    "cart",
    JSON.stringify(cart.value)
  )
}


const addProduct = () => {

  if (!name.value || !price.value) {
    alert("Enter name and price")
    return
  }


  const newProduct = {
    id: Date.now(),      
    name: name.value,
    price: price.value
  }

  products.value.push(newProduct)

  saveProducts()

  name.value = ""
  price.value = ""
}

const deleteProduct = (index) => {
  products.value.splice(index, 1)
  saveProducts()
}

const filteredProducts = computed(() => {
  return products.value.filter(p =>
    p.name
      .toLowerCase()
      .includes(search.value.toLowerCase())
  )
})

const addToCart = (product) => {
  cart.value.push(product)
  saveCart()
}

const removeFromCart = (index) => {
  cart.value.splice(index, 1)
  saveCart()
}

const checkout = () => {
  let total = cart.value.reduce(
    (sum, item) => sum + item.price,
    0
  )


  let vat = total * 0.13


  let grandTotal = total + vat


  alert(
    `Total: Rs ${total}
VAT (13%): Rs ${vat.toFixed(2)}
Grand Total: Rs ${grandTotal.toFixed(2)}`
  )


  cart.value = []
  saveCart()
}
</script>

<style>
.container {
  max-width: 600px;
  margin: auto;
  font-family: Arial;
  color: black;
}


h1 {
  text-align: center;
}


.card {
  background: #f4f4f4;
  padding: 15px;
  margin: 15px 0;
  border-radius: 8px;
}


input {
  padding: 8px;
  margin: 5px;
}


button {
  padding: 6px 10px;
  margin: 5px;
  cursor: pointer;
}


.delete {
  background: red;
  color: white;
}


.checkout {
  background: green;
  color: white;
  width: 100%;
  margin-top: 10px;
}


.product {
  display: flex;
  justify-content: space-between;
  margin: 5px 0;
}
</style>