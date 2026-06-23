<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>BurgerBites Ordering</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background-color: #f9f9f9;
      color: #333;
      text-align: center;
      margin: 0;
      padding: 20px;
      position: relative;
    }
    
    h1 {
      color: #e65100;
      margin-top: 40px;
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
      display: none;
      background-color: #ffe0b2;
      border: 2px dashed #e65100;
      border-radius: 10px;
      padding: 20px;
      margin: 20px auto;
      max-width: 400px;
    }

    .admin-controls button {
      background-color: #2e7d32;
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
      display: flex;
      flex-direction: column;
      justify-content: space-between;
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
      width: 100%;
    }

    button:hover {
      background-color: #e65100;
    }

    .delete-btn {
      display: none; 
      background-color: #d32f2f;
      margin-top: 10px;
    }

    .delete-btn:hover {
      background-color: #b71c1c;
    }

    .admin-mode .delete-btn {
      display: block; 
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

    /* --- CONTACT BUTTONS --- */
    .contact-btn {
      text-decoration: none;
      color: white;
      padding: 12px 20px;
      border-radius: 5px;
      font-weight: bold;
      margin: 10px;
      display: inline-block;
    }

    .call-btn { background-color: #1976D2; }
    .call-btn:hover { background-color: #1565C0; }

    .msg-btn { background-color: #388E3C; }
    .msg-btn:hover { background-color: #2E7D32; }
    
    /* --- SCREENS --- */
    #payment-screen, #success-screen {
      display: none; /* Hidden by default */
      margin-top: 40px;
      background-color: white;
      padding: 40px 20px;
      border-radius: 10px;
      max-width: 500px;
      margin-left: auto;
      margin-right: auto;
      box-shadow: 0 4px 15px rgba(0,0,0,0.1);
    }

    #payment-screen {
      border: 3px solid #ff9800;
    }

    #success-screen {
      border: 3px solid #4CAF50;
    }
  </style>
</head>
<body>

  <button class="admin-btn" onclick="openAdmin()">⚙️ Admin Control</button>

  <div id="admin-panel">
    <h2>🛠️ Admin Dashboard</h2>
    <p><em>Your database is LIVE! You can now add new items or delete existing ones below.</em></p>
    <div class="admin-controls">
      <button onclick="adminAddItem()">➕ Add New Item</button>
    </div>
  </div>

  <h1>🍔 BurgerBites Restaurant</h1>
  
  <div class="menu-container" id="menu-container">
    <p>Loading menu from database...</p>
  </div>

  <div class="cart-section" id="cart-section">
    <h2>🛒 Your Current Order</h2>
    <ul id="order-list">
      <li><em>No items added yet.</em></li>
    </ul>
    <h3>Total: ₹<span id="total-price">0</span></h3>
    
    <h3>📍 Delivery Address</h3>
    <textarea id="customer-address" rows="3" placeholder="Enter your full delivery address here..."></textarea>
    <br>

    <button onclick="showPaymentScreen()">Proceed to Payment ➡️</button>
  </div>

  <div id="payment-screen">
    <h2 style="color: #e65100;">💳 Payment Method</h2>
    <p style="font-size: 1.1em; margin-bottom: 25px;">To ensure a fast and easy experience, we currently only accept Cash on Delivery.</p>
    
    <button onclick="processCOD()" style="background-color: #2E7D32; font-size: 1.3em; padding: 15px; width: 100%;">💵 Cash on Delivery (Confirm Order)</button>
    
    <button onclick="goBackToCart()" style="background-color: #757575; margin-top: 20px; width: auto; padding: 10px 20px;">⬅️ Go Back to Cart</button>
  </div>

  <div id="success-screen">
    <h2 style="color: #2E7D32; font-size: 2em; margin-top: 0;">🎉 Order Created!</h2>
    <p style="font-size: 1.2em;">We have opened WhatsApp for you.</p>
    <p style="font-size: 1.2em; font-weight: bold; color: #d32f2f;">Make sure to press "Send" in WhatsApp to confirm your food!</p>
    
    <div style="margin-top: 30px; padding-top: 20px; border-top: 2px solid #eee;">
      <h3 style="margin-bottom: 20px;">Need Help or Have Questions?</h3>
      <a href="tel:+916000870839" class="contact-btn call-btn">📞 Call Us</a>
      <a href="https://wa.me/916000870839" class="contact-btn msg-btn">💬 Message Us</a>
    </div>

    <button onclick="goBackToMenu()" style="background-color: #333; margin-top: 40px; width: auto; padding: 10px 20px;">⬅️ Go Back to Menu</button>
  </div>

  <script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.0/firebase-app.js";
    import { getFirestore, collection, addDoc, onSnapshot, doc, deleteDoc } from "https://www.gstatic.com/firebasejs/10.12.0/firebase-firestore.js";

    const firebaseConfig = {
      apiKey: "AIzaSyBg0OfggCb2vWQFuCPaUBtFCXG5_fh3GuQ",
      authDomain: "burgerbites-7299e.firebaseapp.com",
      projectId: "burgerbites-7299e",
      storageBucket: "burgerbites-7299e.firebasestorage.app",
      messagingSenderId: "125414622245",
      appId: "1:125414622245:web:20e519ee02fb7f0a53abcc",
      measurementId: "G-FHKNFZCRZK"
    };

    const app = initializeApp(firebaseConfig);
    const db = getFirestore(app);

    const menuContainer = document.getElementById('menu-container');
    
    onSnapshot(collection(db, "menuItems"), (snapshot) => {
      menuContainer.innerHTML = ""; 
      
      if (snapshot.empty) {
        menuContainer.innerHTML = "<p>Menu is empty! Please log into Admin Control to add food items.</p>";
      }

      snapshot.forEach((firestoreDoc) => {
        let item = firestoreDoc.data();
        let docId = firestoreDoc.id; 

        let newDiv = document.createElement("div");
        newDiv.className = "menu-item";
        
        newDiv.innerHTML = `
          <div>
            <h2>${item.name}</h2>
            <p class="price">₹${item.price}</p>
          </div>
          <div>
            <button onclick="addToOrder('${item.name}', ${item.price})">Add to Order</button>
            <button class="delete-btn" onclick="deleteItem('${docId}', '${item.name}')">🗑️ Delete</button>
          </div>
        `;
        menuContainer.appendChild(newDiv);
      });
    });

    let orderItems = [];
    let orderTotal = 0;

    window.addToOrder = function(itemName, itemPrice) {
      orderItems.push(itemName);
      orderTotal += itemPrice;
      window.updateCartDisplay();
    };

    window.updateCartDisplay = function() {
      const listElement = document.getElementById('order-list');
      listElement.innerHTML = ""; 

      for (let i = 0; i < orderItems.length; i++) {
        let listItem = document.createElement('li');
        listItem.innerText = orderItems[i];
        listElement.appendChild(listItem);
      }
      document.getElementById('total-price').innerText = orderTotal;
    };

    // --- STEP 1: VALIDATE AND SHOW PAYMENT SCREEN ---
    window.showPaymentScreen = function() {
      if (orderItems.length === 0) {
        alert("Please add some food to your order first!");
        return;
      }
      const addressInput = document.getElementById('customer-address').value.trim();
      if (addressInput === "") {
        alert("Please enter your delivery address!");
        return;
      }

      // Hide Menu and Cart
      document.getElementById('menu-container').style.display = "none";
      document.getElementById('cart-section').style.display = "none";
      
      // Show Payment Screen
      document.getElementById('payment-screen').style.display = "block";
    };

    // --- STEP 2: PROCESS COD AND OPEN WHATSAPP ---
    window.processCOD = function() {
      const addressInput = document.getElementById('customer-address').value.trim();

      let orderMessage = "🍔 NEW RESTAURANT ORDER 🍔\n\n";
      for (let i = 0; i < orderItems.length; i++) {
        orderMessage += "- " + orderItems[i] + "\n";
      }
      orderMessage += "\n💰 Total: ₹" + orderTotal;
      orderMessage += "\n💳 Payment Mode: Cash on Delivery";
      orderMessage += "\n\n📍 Delivery Address:\n" + addressInput;

      const myPhoneNumber = "916000870839"; 
      const encodedMessage = encodeURIComponent(orderMessage);
      const whatsappUrl = "https://wa.me/" + myPhoneNumber + "?text=" + encodedMessage;

      // 1. Open WhatsApp
      window.open(whatsappUrl, '_blank');

      // 2. Hide Payment Screen, Show Success Screen
      document.getElementById('payment-screen').style.display = "none";
      document.getElementById('success-screen').style.display = "block";

      // 3. Clear the Cart Data
      orderItems = [];
      orderTotal = 0;
      document.getElementById('order-list').innerHTML = "<li><em>No items added yet.</em></li>";
      document.getElementById('total-price').innerText = "0";
      document.getElementById('customer-address').value = "";
    };

    // --- HELPER FUNCTIONS FOR NAVIGATION ---
    window.goBackToCart = function() {
      // Hide payment screen, bring back menu and cart
      document.getElementById('payment-screen').style.display = "none";
      document.getElementById('menu-container').style.display = "flex";
      document.getElementById('cart-section').style.display = "inline-block";
    };

    window.goBackToMenu = function() {
      // Hide success screen, bring back menu and cart
      document.getElementById('success-screen').style.display = "none";
      document.getElementById('menu-container').style.display = "flex";
      document.getElementById('cart-section').style.display = "inline-block";
    };

    // --- ADMIN SYSTEM ---
    const ADMIN_PIN = "3645"; 

    window.openAdmin = function() {
      let enteredPin = prompt("Please enter the Admin PIN:");
      if (enteredPin === ADMIN_PIN) {
        document.getElementById('admin-panel').style.display = "block";
        document.body.classList.add('admin-mode'); 
      } else if (enteredPin !== null) {
        alert("Incorrect PIN! Access Denied.");
      }
    };

    window.adminAddItem = async function() {
      let newItemName = prompt("Enter the name of the new food item:");
      if (!newItemName) return; 

      let newItemPrice = prompt("Enter the price in Rupees (Numbers only!):");
      if (!newItemPrice || isNaN(newItemPrice)) {
        alert("Invalid price! Please enter numbers only.");
        return;
      }

      let priceNumber = parseInt(newItemPrice);

      try {
        await addDoc(collection(db, "menuItems"), {
          name: newItemName,
          price: priceNumber
        });
      } catch (error) {
        alert("Error saving item: " + error.message);
      }
    };

    window.deleteItem = async function(docId, itemName) {
      let confirmDelete = confirm("Are you sure you want to permanently delete " + itemName + "?");
      if (confirmDelete) {
        try {
          await deleteDoc(doc(db, "menuItems", docId));
        } catch (error) {
          alert("Error deleting item: " + error.message);
        }
      }
    };
  </script>

</body>
</html>

            
