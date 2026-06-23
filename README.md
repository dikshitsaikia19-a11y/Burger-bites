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

    /* DELETE BUTTON (Hidden by default) */
    .delete-btn {
      display: none; 
      background-color: #d32f2f;
      margin-top: 10px;
    }

    .delete-btn:hover {
      background-color: #b71c1c;
    }

    /* When Admin is logged in, show the delete buttons! */
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
  <p>Click "Add to Order" to choose your food, then send it to our kitchen!</p>

  <div class="menu-container" id="menu-container">
    <p>Loading menu from database...</p>
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

  <script type="module">
    // 1. Import Firebase tools (Added deleteDoc and doc)
    import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.0/firebase-app.js";
    import { getFirestore, collection, addDoc, onSnapshot, doc, deleteDoc } from "https://www.gstatic.com/firebasejs/10.12.0/firebase-firestore.js";

    // 2. Your Firebase Keys
    const firebaseConfig = {
      apiKey: "AIzaSyBg0OfggCb2vWQFuCPaUBtFCXG5_fh3GuQ",
      authDomain: "burgerbites-7299e.firebaseapp.com",
      projectId: "burgerbites-7299e",
      storageBucket: "burgerbites-7299e.firebasestorage.app",
      messagingSenderId: "125414622245",
      appId: "1:125414622245:web:20e519ee02fb7f0a53abcc",
      measurementId: "G-FHKNFZCRZK"
    };

    // 3. Start Firebase and Database
    const app = initializeApp(firebaseConfig);
    const db = getFirestore(app);

    // --- LOAD MENU FROM FIREBASE IN REAL-TIME ---
    const menuContainer = document.getElementById('menu-container');
    
    onSnapshot(collection(db, "menuItems"), (snapshot) => {
      menuContainer.innerHTML = ""; 
      
      if (snapshot.empty) {
        menuContainer.innerHTML = "<p>Menu is empty! Please log into Admin Control to add food items.</p>";
      }

      snapshot.forEach((firestoreDoc) => {
        let item = firestoreDoc.data();
        let docId = firestoreDoc.id; // Get the specific Firebase ID for deleting

        let newDiv = document.createElement("div");
        newDiv.className = "menu-item";
        
        // Added the hidden delete button that passes the unique ID
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

    // --- CART SYSTEM ---
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

    window.sendToKitchen = function() {
      if (orderItems.length === 0) {
        alert("Please add some food to your order first!");
        return;
      }
      const addressInput = document.getElementById('customer-address').value.trim();
      if (addressInput === "") {
        alert("Please enter your delivery address!");
        return;
      }

      let orderMessage = "🍔 NEW RESTAURANT ORDER 🍔\n\n";
      for (let i = 0; i < orderItems.length; i++) {
        orderMessage += "- " + orderItems[i] + "\n";
      }
      orderMessage += "\n💰 Total: ₹" + orderTotal;
      orderMessage += "\n\n📍 Delivery Address:\n" + addressInput;

      const myPhoneNumber = "916000870839"; 
      const encodedMessage = encodeURIComponent(orderMessage);
      const whatsappUrl = "https://wa.me/" + myPhoneNumber + "?text=" + encodedMessage;

      window.open(whatsappUrl, '_blank');

      orderItems = [];
      orderTotal = 0;
      document.getElementById('order-list').innerHTML = "<li><em>No items added yet.</em></li>";
      document.getElementById('total-price').innerText = "0";
      document.getElementById('customer-address').value = "";
    };

    // --- ADMIN SYSTEM ---
    const ADMIN_PIN = "3645"; 

    window.openAdmin = function() {
      let enteredPin = prompt("Please enter the Admin PIN:");
      if (enteredPin === ADMIN_PIN) {
        document.getElementById('admin-panel').style.display = "block";
        
        // This makes all the hidden delete buttons appear!
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
        // No alert needed, it will magically pop onto the screen!
      } catch (error) {
        alert("Error saving item: " + error.message);
      }
    };

    // --- NEW: DELETE ITEM FUNCTION ---
    window.deleteItem = async function(docId, itemName) {
      // Ask for confirmation so you don't accidentally delete something
      let confirmDelete = confirm("Are you sure you want to permanently delete " + itemName + "?");
      
      if (confirmDelete) {
        try {
          await deleteDoc(doc(db, "menuItems", docId));
          // Firebase will automatically remove it from the screen!
        } catch (error) {
          alert("Error deleting item: " + error.message);
        }
      }
    };
  </script>

</body>
</html>
    
    
