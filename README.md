<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>E-commerce Without Login</title>
<style>
  body { font-family: Arial, sans-serif; margin:0; padding:0; background:#f9f9f9; }
  header { background: #007bff; color: white; padding: 10px 20px; display: flex; justify-content: space-between; align-items: center; flex-wrap: wrap; }
  header h1 { margin: 0; }
  #searchBar { padding: 5px; width: 200px; margin: 5px 10px; border-radius: 4px; border: none; }
  #cartToggle { cursor: pointer; background: white; border: none; color: #007bff; font-weight: bold; padding: 8px 12px; border-radius: 3px; margin: 5px 5px; }
  #cartToggle:hover { background: #e6e6e6; }
  #products { display: grid; grid-template-columns: repeat(auto-fit,minmax(220px,1fr)); gap: 15px; padding: 20px; }
  .product { background: white; border: 1px solid #ccc; padding: 10px; border-radius: 5px; box-shadow: 1px 1px 5px rgba(0,0,0,0.1); }
  .product img { width: 100%; height: 150px; object-fit: contain; }
  .product h3 { margin: 10px 0 5px; }
  .product p { margin: 5px 0; }
  .product button { background: #28a745; color: white; border: none; padding: 8px; width: 100%; cursor: pointer; border-radius: 3px; font-weight: bold; }
  .product button:hover { background: #218838; }
  
  #cart {
    position: fixed; top: 10px; right: 10px; background: white; border: 1px solid #007bff; padding: 15px; width: 320px; max-height: 480px; 
    overflow-y: auto; border-radius: 5px; display: none; box-shadow: 0 0 15px rgba(0,123,255,.3);
    z-index: 100;
  }
  #cart h2 { margin-top: 0; }
  #cartItems { list-style: none; padding: 0; margin: 0; }
  #cartItems li {
    display: flex; justify-content: space-between; align-items: center; margin-bottom: 8px;
    border-bottom: 1px solid #eee; padding-bottom: 5px;
  }
  #cartItems li div {
    display: flex; align-items: center; gap: 5px;
  }
  #cartItems li button {
    background: red; border: none; color: white; cursor: pointer; border-radius: 3px; padding: 2px 6px;
  }
  #cartTotal { font-weight: bold; margin-top: 10px; }
  #checkoutBtn, #clearCartBtn {
    background: #ffc107; border: none; padding: 8px; width: 100%; cursor: pointer; border-radius: 3px; margin-top: 10px; font-weight: bold;
  }
  #checkoutBtn:hover, #clearCartBtn:hover {
    background: #e0a800;
  }

  /* Checkout Modal */
  .modal {
    display: none; position: fixed; z-index: 200; left: 0; top: 0; width: 100%; height: 100%; overflow: auto; 
    background-color: rgba(0,0,0,0.4); justify-content: center; align-items: center;
  }
  .modal-content {
    background-color: #fefefe; margin: auto; padding: 20px; border: 1px solid #888; border-radius: 8px; width: 90%; max-width: 400px;
    box-shadow: 0 0 10px rgba(0,0,0,.25);
  }
  .close {
    color: #aaa; float: right; font-size: 28px; font-weight: bold; cursor: pointer;
  }
  .close:hover, .close:focus {
    color: black; text-decoration: none; cursor: pointer;
  }
  label { display: block; margin-top: 10px; font-weight: bold; }
  input[type="text"], input[type="number"], select {
    width: 100%; padding: 8px; margin-top: 4px; box-sizing: border-box; border-radius: 4px; border: 1px solid #ccc;
  }
  button.submitBtn {
    background: #007bff; color: white; border: none; padding: 10px; width: 100%; margin-top: 15px; cursor: pointer; border-radius: 4px;
  }
  button.submitBtn:hover {
    background: #0056b3;
  }

  /* Responsive */
  @media (max-width: 600px) {
    #cart { width: 90%; right: 5%; }
  }
</style>
</head>
<body>

<header>
  <h1>My Shop</h1>
  <input type="text" id="searchBar" placeholder="Search products..." />
  <button id="cartToggle">Cart (0)</button>
</header>

<section id="products"></section>

<div id="cart">
  <h2>Your Cart</h2>
  <ul id="cartItems"></ul>
  <div id="cartTotal">Total: $0.00</div>
  <button id="checkoutBtn">Checkout</button>
  <button id="clearCartBtn">Clear Cart</button>
</div>

<!-- Checkout Modal -->
<div id="checkoutModal" class="modal">
  <div class="modal-content">
    <span class="close" id="checkoutClose">&times;</span>
    <h2>Checkout</h2>
    <form id="checkoutForm">
      <label for="name">Full Name:</label>
      <input type="text" id="name" required />
      <label for="address">Shipping Address:</label>
      <input type="text" id="address" required />
      <label for="phone">Phone Number:</label>
      <input type="text" id="phone" required pattern="^[0-9+ -]{7,15}$" />
      <label for="payment">Payment Method:</label>
      <select id="payment" required>
        <option value="">Select</option>
        <option value="cod">Cash on Delivery</option>
        <option value="jazzcash">JazzCash</option>
        <option value="easypaisa">EasyPaisa</option>
      </select>
      <button type="submit" class="submitBtn">Place Order</button>
    </form>
  </div>
</div>

<footer>
  &copy; 2025 My Shop. All rights reserved.
</footer>

<script>
  const products = [
    { id: 1, name: 'Blue T-Shirt', price: 20, image: 'https://via.placeholder.com/200x150?text=Blue+T-Shirt' },
    { id: 2, name: 'Red Sneakers', price: 50, image: 'https://via.placeholder.com/200x150?text=Red+Sneakers' },
    { id: 3, name: 'Smart Watch', price: 120, image: 'https://via.placeholder.com/200x150?text=Smart+Watch' },
    { id: 4, name: 'Leather Wallet', price: 35, image: 'https://via.placeholder.com/200x150?text=Leather+Wallet' },
    { id: 5, name: 'Jeans', price: 40, image: 'https://via.placeholder.com/200x150?text=Jeans' }
  ];

  const productsContainer = document.getElementById('products');
  const searchBar = document.getElementById('searchBar');
  const cartToggle = document.getElementById('cartToggle');
  const cartElement = document.getElementById('cart');
  const cartItemsList = document.getElementById('cartItems');
  const cartTotal = document.getElementById('cartTotal');
  const checkoutBtn = document.getElementById('checkoutBtn');
  const clearCartBtn = document.getElementById('clearCartBtn');

  const checkoutModal = document.getElementById('checkoutModal');
  const checkoutClose = document.getElementById('checkoutClose');
  const checkoutForm = document.getElementById('checkoutForm');

  let cart = JSON.parse(localStorage.getItem('cart')) || [];

  function saveCart() {
    localStorage.setItem('cart', JSON.stringify(cart));
  }

  function renderProducts(filter = '') {
    productsContainer.innerHTML = '';
    const filtered = products.filter(p => p.name.toLowerCase().includes(filter.toLowerCase()));
    if (filtered.length === 0) {
      productsContainer.innerHTML = '<p>No products found.</p>';
      return;
    }
    filtered.forEach(product => {
      const div = document.createElement('div');
      div.className = 'product';
      div.innerHTML = `
        <img src="${product.image}" alt="${product.name}" />
        <h3>${product.name}</h3>
        <p>Price: $${product.price.toFixed(2)}</p>
        <button data-id="${product.id}">Add to Cart</button>
      `;
      productsContainer.appendChild(div);
    });
  }

  function updateCartUI() {
    cartItemsList.innerHTML = '';
    if (cart.length === 0) {
      cartItemsList.innerHTML = '<li>Your cart is empty.</li>';
      cartTotal.textContent = 'Total: $0.00';
      cartToggle.textContent = 'Cart (0)';
      return;
    }
    let total = 0;
    cart.forEach(item => {
      total += item.price * item.qty;
      const li = document.createElement('li');
      li.innerHTML = `
        <div>
          ${item.name} x
          <button class="qtyBtn" data-id="${item.id}" data-action="minus">−</button>
          <span>${item.qty}</span>
          <button class="qtyBtn" data-id="${item.id}" data-action="plus">+</button>
        </div>
        <div>
          $${(item.price * item.qty).toFixed(2)} 
          <button class="removeBtn" data-id="${item.id}" title="Remove">&times;</button>
        </div>
      `;
      cartItemsList.appendChild(li);
    });
    cartTotal.textContent = `Total: $${total.toFixed(2)}`;
    cartToggle.textContent = `Cart (${cart.reduce((acc, i) => acc + i.qty, 0)})`;
    saveCart();
  }

  function addToCart(id) {
    const product = products.find(p => p.id === id);
    if (!product) return;
    const cartItem = cart.find(i => i.id === id);
    if (cartItem) {
      cartItem.qty++;
    } else {
      cart.push({...product, qty: 1});
    }
    updateCartUI();
  }

  function removeFromCart(id) {
    cart = cart.filter(item => item.id !== id);
    updateCartUI();
  }

  function changeQuantity(id, action) {
    const item = cart.find(i => i.id === id);
    if (!item) return;
    if (action === 'plus') {
      item.qty++;
    } else if (action === 'minus') {
      if (item.qty > 1) {
        item.qty--;
      } else {
        removeFromCart(id);
        return;
      }
    }
    updateCartUI();
  }

  // Toggle Cart
  cartToggle.addEventListener('click', () => {
    if (cartElement.style.display === 'block') {
      cartElement.style.display = 'none';
    } else {
      cartElement.style.display = 'block';
    }
  });

  // Add to cart buttons
  productsContainer.addEventListener('click', e => {
    if (e.target.tagName === 'BUTTON') {
      const id = parseInt(e.target.getAttribute('data-id'));
      addToCart(id);
    }
  });

  // Cart buttons (remove, qty)
  cartItemsList.addEventListener('click', e => {
    if (e.target.classList.contains('removeBtn')) {
      const id = parseInt(e.target.getAttribute('data-id'));
      removeFromCart(id);
    } else if (e.target.classList.contains('qtyBtn')) {
      const id = parseInt(e.target.getAttribute('data-id'));
      const action = e.target.getAttribute('data-action');
      changeQuantity(id, action);
    }
  });

  // Clear Cart
  clearCartBtn.addEventListener('click', () => {
    cart = [];
    updateCartUI();
  });

  // Checkout modal toggle
  checkoutBtn.addEventListener('click', () => {
    if (cart.length === 0) {
      alert('Your cart is empty!');
      return;
    }
    checkoutModal.style.display = 'flex';
  });

  checkoutClose.addEventListener('click', () => {
    checkoutModal.style.display = 'none';
  });

  // Checkout form submission
  checkoutForm.addEventListener('submit', e => {
    e.preventDefault();
    alert('Order placed successfully! Thank you for shopping.');
    cart = [];
    updateCartUI();
    checkoutModal.style.display = 'none';
    checkoutForm.reset();
  });

  // Close modal on clicking outside modal-content
  window.addEventListener('click', e => {
    if (e.target === checkoutModal) {
      checkoutModal.style.display = 'none';
    }
  });

  // Search products
  searchBar.addEventListener('input', () => {
    renderProducts(searchBar.value);
  });

  // Initial render
  renderProducts();
  updateCartUI();
</script>

</body>
</html>
