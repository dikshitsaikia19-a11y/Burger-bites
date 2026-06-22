<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Delicious Bites Ordering</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background-color: #f9f9f9;
      color: #333;
      text-align: center;
      margin: 0;
      padding: 20px;
      position: relative; /* Needed for the admin button positioning */
    }
    
    h1 {
      color: #e65100;
      margin-top: 40px; /* Pushed down slightly to make room for admin button */
    }

    /* --- ADMIN BUTTON STYLES --- */
    .admin-btn {
      position: absolute;
      top: 10px;
      right: 20px;
      background-color: #333;
      color: white;
      border: none;
      padding: 8px 12px;
      border-radius: 5px;
      cursor: pointer;
      font-weight: bold;
    }

    .admin-btn:hover {
      background-color: #000;
    }

    #admin-panel {
      display: none; /* Hidden by default */
      background-color: #ffe0b2;
      border: 2px dashed #e65100;
      border-radius: 10px;
      padding: 20px;
      margin: 20px auto;
      max-width: 400px;
    }

    .admin-controls button {
      background-color: #d32f2f;
      margin: 5px;
    }
    /* --------------------------- */

    .menu-container {
      display: flex;
      justify-content: center;
      gap: 20px;
      flex-wrap: wrap;
      margin-top: 20px;
    }

    .menu-item {
      background-color: white;
      border: 2px solid #ddd;
      border-radius: 10px;
      padding: 20px;
      width: 200px;
      box-shadow: 0 4px 8px rgba(0,0,0,0.1);
    }

    .price {
      font-weight: bold;
      color: #2e7d32;
      font-size: 1.2em;
    }

    button {
      background-color: #ff9800;
      color: white;
      border: none;
      padding: 10px 15px;
      border-radius: 5px;
      cursor: pointer;
      font-weight: bold;
      margin-top: 10px;
    }

    button:hover {
      background-color: #e65100;
    }

    .cart-section {
      margin-top: 40px;
      background-color: white;
      padding: 20px;
      border-radius: 10px;
      display: inline-block;
      min-width: 300px;
      border: 2px dashed #ff9800;
    }

    ul {
      list-style-type: none;
      padding: 0;
    }

    li {
      padding: 5px 0;
      border-bottom: 1px solid #eee;
    }

    textarea {
      width: 100%;
      max-width: 280px;
      padding: 10px;
      margin-top: 10px;
      margin-bottom: 15px;
      border-radius: 5px;
      border: 1px solid #ccc;
      font-family: inherit;
    }
  </style>
</head>
<body>

  <button class="admin-btn" onclick="openAdmin()">⚙️ Admin Control</button>

  <div id="admin-panel">
    <h2>🛠️ Admin Dashboard</h2>
    <p><em>Note: Since this runs without a database, items added here will disappear if the page is refreshed. To make them permanent, edit the code directly!</em></p>
    <div class="admin-controls">
      <button onclick="adminAddItem()">➕ Add New Item</button>
    </div>
  </div>

  <h1>🍔 Delicious Bites Restaurant</h1>
  <p>Click "Add to Order" to choose your food, then send it to our kitchen!</p>

  <div class="menu-container" id="menu-container">
    <div class="menu-item">
      <h2>Cheese Pizza</h2>
      <p class="price">₹250</p>
      <button onclick="addToOrder('Cheese Pizza', 250)">Add to Order</button>
    </div>

    <div class="menu-item">
      <h2>Veggie Burger</h2>
      <p class="price">₹120</p>
      <button onclick="addToOrder('Veggie Burger', 120)">Add to Order</button>
    </div>

    <div class="menu-item">
      <h2>French Fries</h2>
      <p class="price">₹90</p>
      <button onclick="addToOrder('French Fries', 90)">Add to Order</button>
    </div>
  </div>

  <div class="cart-section">
    <h2>🛒 Your Current Order</h2>
    <ul id="order-list">
      <li><em>No items added yet.</em></li>
    </ul>
    <h3>Total: ₹<span id="total-price">0</span></h3>
    
    <h3>📍 Delivery Address</h3>
    <textarea id="customer-address" rows="3" placeholder="Enter your full delivery address here..."></textarea>
    <br>

    <button onclick="sendToKitchen()">Send Order via WhatsApp</button>
  </div>

  <script>
    // --- ADMIN JAVASCRIPT ---
    const ADMIN_PIN = "3645"; // <--- YOUR CUSTOM PIN IS SET HERE

    function openAdmin() {
      let enteredPin = prompt("Please enter the Admin PIN:");
      
      if (enteredPin === ADMIN_PIN) {
        document.getElementById('admin-panel').style.display = "block";
      } else if (enteredPin !== null) {
        alert("Incorrect PIN! Access Denied.");
      }
    }

    function adminAddItem() {
      let newItemName = prompt("Enter the name of the new food item:");
      if (!newItemName) return; // Stop if they cancel

      let newItemPrice = prompt("Enter the price in Rupees (Numbers only!):");
      if (!newItemPrice || isNaN(newItemPrice)) {
        alert("Invalid price! Please enter numbers only.");
        return;
      }

      // Convert price to a number
      let priceNumber = parseInt(newItemPrice);

      // Create the new HTML block for the item
      let newDiv = document.createElement("div");
      newDiv.className = "menu-item";
      newDiv.innerHTML = `
        <h2>${newItemName}</h2>
        <p class="price">₹${priceNumber}</p>
        <button onclick="addToOrder('${newItemName}', ${priceNumber})">Add to Order</button>
      `;

      // Add it to the screen dynamically
      document.getElementById('menu-container').appendChild(newDiv);
      alert(newItemName + " has been added to the menu successfully!");
    }
    // ------------------------

    // --- CART AND WHATSAPP JAVASCRIPT ---
    let orderItems = [];
    let orderTotal = 0;

    function addToOrder(itemName, itemPrice) {
      orderItems.push(itemName);
      orderTotal += itemPrice;
      updateCartDisplay();
    }

    function updateCartDisplay() {
      const listElement = document.getElementById('order-list');
      listElement.innerHTML = ""; 

      for (let i = 0; i < orderItems.length; i++) {
        let listItem = document.createElement('li');
        listItem.innerText = orderItems[i];
        listElement.appendChild(listItem);
      }

      document.getElementById('total-price').innerText = orderTotal;
    }

    function sendToKitchen() {
      if (orderItems.length === 0) {
        alert("Please add some food to your order first!");
        return;
      }

      const addressInput = document.getElementById('customer-address').value.trim();
      if (addressInput === "") {
        alert("Please enter your delivery address!");
        return;
      }

      // Format the WhatsApp message
      let orderMessage = "🍔 NEW RESTAURANT ORDER 🍔\n\n";
      for (let i = 0; i < orderItems.length; i++) {
        orderMessage += "- " + orderItems[i] + "\n";
      }
      orderMessage += "\n💰 Total: ₹" + orderTotal;
      orderMessage += "\n\n📍 Delivery Address:\n" + addressInput;

      // YOUR ACTUAL WHATSAPP NUMBER IS SET HERE:
      const myPhoneNumber = "916000870839"; 

      // Create link and open WhatsApp
      const encodedMessage = encodeURIComponent(orderMessage);
      const whatsappUrl = "https://wa.me/" + myPhoneNumber + "?text=" + encodedMessage;

      window.open(whatsappUrl, '_blank');

      // Clear the cart after sending
      orderItems = [];
      orderTotal = 0;
      document.getElementById('order-list').innerHTML = "<li><em>No items added yet.</em></li>";
      document.getElementById('total-price').innerText = "0";
      document.getElementById('customer-address').value = "";
    }
  </script>

</body>
</html>
      
