<template>
  <div class="main-container">
    <h1>Mini E-Commerce</h1>

  <div class="card-card card add-input-column">
  <h2>Add Product</h2> <br />

  <label>Product Name</label>
  <input v-model="name" placeholder="Enter product name" />

  <label>Price (Rs)</label>
  <input
    v-model.number="price"
    type="number"
    placeholder="Enter price"
    min="0"
  />

  <label>Quantity</label>
  <input
    v-model.number="quantity"
    type="number"
    placeholder="Enter quantity"
    min="1"
  />

  <button @click="addProduct">Add</button>
</div>

    <div class="card">
      <h2>Products</h2>
      <input v-model="search" placeholder="Search product..." />
      <table class="product-table">
        <thead>
          <tr>
            <th>Product Name</th>
            <th>Per Price</th>
            <th>In Stock</th>
            <th>Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(product, index) in filteredProducts" :key="product.id">
            <td>{{ product.name }}</td>
            <td>Rs {{ product.price }}</td>
            <td>
              {{ product.quantity }}</td>
            <td>
              <div class="action-buttons">
                <button @click="addToCart(product)" :disabled="!product.quantity || product.quantity < 1">Add to Cart</button>
                <button class="delete" @click="deleteProduct(index)">Delete</button>
              </div>
            </td>
          </tr>
          
<tr v-if="filteredProducts.length === 0"class="no-products-msg">
  <td colspan="4" style="color: white; font-weight: bold; margin-top: 5px; font-size: 0.9rem;">
    No products found
  </td>
</tr>
        </tbody>
      </table>
    </div>

    <div class="card">
      <h2>Cart({{ cart.length }})</h2>
      <input v-model="search" placeholder="Search cartItem..." />

      <div v-if="cart.length === 0">Cart is empty</div>

      <table border="1" class="product-table">
        <thead>
          <tr>
            <th>Product name</th>
            <th>Quantity</th>
            <th>per price</th>
            <th>Total</th>
            <th>Action</th>
          </tr>
        </thead>
        <tbody>

          <tr v-for = "(item,index) in cart" :key="index">
            <td>{{ item.name }}</td>
            <td>{{ item.quantity || 1 }}</td>
            <td>Rs {{ item.price }}</td>
            <td>Rs {{ (item.price * (item.quantity || 1)) }}</td>
            <td>
              <button class="delete" @click="removeFromCart(index)">Remove</button>
            </td> 
          </tr>
        </tbody>
      </table>


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
const quantity = ref(1)
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
  saveProducts();
  localStorage.setItem(
    "cart",
    JSON.stringify(cart.value)
  );
}
const blockNegative = (e) => {
  if (e.key === "-" || e.key === "e") {
    e.preventDefault()
  }
}

const fixNegative = () => {
  if (price.value < 0) {
    price.value = 0
  }
}
const addProduct = () => {
  if (!name.value.trim()) {
    alert("Enter product name")
    return
  }

  if (price.value === "" || price.value === null) {
    alert("Enter price")
    return
  }

  if (price.value <= 0) {
    alert("Price must be greater than 0")
    return
  }

  if (quantity.value === "" || quantity.value === null || quantity.value < 1) {
    alert("Enter a valid quantity (at least 1)")
    return
  }

  const newProduct = {
    id: Date.now(),
    name: name.value,
    price: Number(price.value),
    quantity: Number(quantity.value)
  }

  products.value.push(newProduct)
  saveProducts()

  name.value = ""
  price.value = ""
  quantity.value = 1
}

const deleteProduct = (index) => {
  const productId = products.value[index].id;
  const isIncart = cart.value.some(item=>item.id === productId)

  if (isIncart){
    alert("cannot delete product that is in cart")
    return;
  }

  products.value.splice(index,1)
  cart.value = cart.value.filter(item => item.id !== productId)
  saveProducts()
  saveCart()
}

const removeItem = () => {
  emit('remove-item', props.item.id); 
};



const filteredProducts = computed(() => {
  return products.value.filter(p =>
    p.name
      .toLowerCase()
      .includes(search.value.toLowerCase())
  )
})

const addToCart = (product) => {

  const prod = products.value.find(p => p.id === product.id)
  if (!prod) return;

  if (!prod.quantity || prod.quantity < 1) {
    return;
  }
 
  prod.quantity = prod.quantity - 1;
  const cartItem = cart.value.find(item => item.id === product.id)
  if (cartItem) {
    cartItem.quantity = cartItem.quantity + 1;
  } else {
    cart.value.push({ id: product.id, name: product.name, price: product.price, quantity: 1 });
  }
  saveCart();
}

const removeFromCart = (index) => {
  const item = cart.value[index];
  if (item) {
    const prod = products.value.find(p => p.id === item.id);
    if (prod) {
      prod.quantity = (prod.quantity || 0) + (item.quantity || 1);
    }
  }
  cart.value.splice(index, 1);
  saveCart();
}

const checkout = () => {
  let total = cart.value.reduce(
    (sum, item) => sum + (item.price * (item.quantity || 1)),
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
*{
  color: black;
}
.product-table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 10px;
}
.product-table th, .product-table td {
  border: 1px solid #ccc;
  padding: 8px;
  text-align: center;
}
.product-table th {
  background: red;
}
.action-buttons {
  display: flex;
  flex-direction: column;
  gap: 5px;
}
.container {
  max-width: 600px;
  margin: auto;
  font-family: Arial;
  color: black;

.main-container {
  max-width: 600px;
  width: 100%;
  margin: 40px auto;
  display: flex;
  flex-direction: column;
  align-items: center;
}
}

.add-input-column {
  display: flex;          
  flex-direction: column; 
  gap: 8px;     
}          

h1 {
  text-align: center;
}


.card {
  background: #718093;
  padding: 15px;
  margin: 15px 0;
  border-radius: 8px;
}


input {
  padding: 8px;
  margin: 5px;
  border-radius: 5px;
  border: 1px solid #ccc;
}


button {
  padding: 6px 10px;
  margin: 5px;
  cursor: pointer;
  color: white;
  background: rgb(255 255 255 / 21%);

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