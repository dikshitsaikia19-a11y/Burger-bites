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
    
    h1 { color: #e65100; margin-top: 20px; }

    /* --- LOGIN SCREEN STYLES --- */
    #login-screen {
      background-color: white;
      border: 3px solid #1976D2;
      border-radius: 10px;
      padding: 40px 20px;
      max-width: 400px;
      margin: 60px auto;
      box-shadow: 0 4px 15px rgba(0,0,0,0.1);
    }

    .google-btn {
      background-color: #4285F4;
      color: white;
      font-size: 1.1em;
      padding: 12px;
      width: 100%;
      border-radius: 5px;
      margin-bottom: 20px;
    }

    .google-btn:hover { background-color: #3367D6; }

    input[type="tel"], input[type="text"] {
      width: 90%;
      padding: 10px;
      margin-top: 10px;
      margin-bottom: 10px;
      border-radius: 5px;
      border: 1px solid #ccc;
      font-size: 1.1em;
    }

    #recaptcha-container {
      margin-top: 15px;
      display: flex;
      justify-content: center;
    }

    /* --- MAIN APP STYLES --- */
    #main-app { display: none; /* Hidden until logged in */ }

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

    .admin-btn:hover { background-color: #000; }

    #admin-panel {
      display: none;
      background-color: #ffe0b2;
      border: 2px dashed #e65100;
      border-radius: 10px;
      padding: 20px;
      margin: 20px auto;
      max-width: 400px;
    }

    .admin-controls button { background-color: #2e7d32; margin: 5px; }

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

    .price { font-weight: bold; color: #2e7d32; font-size: 1.2em; }

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

    button:hover { background-color: #e65100; }

    .delete-btn { display: none; background-color: #d32f2f; margin-top: 10px; }
    .delete-btn:hover { background-color: #b71c1c; }
    .admin-mode .delete-btn { display: block; }

    .cart-section {
      margin-top: 40px;
      background-color: white;
      padding: 20px;
      border-radius: 10px;
      display: inline-block;
      min-width: 300px;
      border: 2px dashed #ff9800;
    }

    ul { list-style-type: none; padding: 0; }
    li { padding: 5px 0; border-bottom: 1px solid #eee; }
    textarea { width: 100%; max-width: 280px; padding: 10px; margin-top: 10px; border: 1px solid #ccc; font-family: inherit; }

    .contact-btn { text-decoration: none; color: white; padding: 12px 20px; border-radius: 5px; font-weight: bold; margin: 10px; display: inline-block; }
    .call-btn { background-color: #1976D2; }
    .msg-btn { background-color: #388E3C; }

    #payment-screen, #success-screen {
      display: none; margin-top: 40px; background-color: white; padding: 40px 20px; border-radius: 10px; max-width: 500px; margin-left: auto; margin-right: auto; box-shadow: 0 4px 15px rgba(0,0,0,0.1);
    }
    #payment-screen { border: 3px solid #ff9800; }
    #success-screen { border: 3px solid #4CAF50; }
  </style>
</head>
<body>

  <div id="login-screen">
    <h2>🍔 Welcome to BurgerBites!</h2>
    <p>Please log in securely to view our menu and place your order.</p>
    
    <button class="google-btn" onclick="loginWithGoogle()">Sign in with Google</button>
    
    <hr style="margin: 20px 0; border: 1px solid #eee;">
    
    <p style="font-weight: bold;">Or log in with Phone Number:</p>
    <input type="tel" id="phone-number" placeholder="+91 60000 00000" />
    <button onclick="sendOTP()" style="background-color: #333;">Send Verification Code</button>
    
    <div id="recaptcha-container"></div>

    <div id="otp-section" style="display: none; margin-top: 20px; border-top: 2px dashed #ccc; padding-top: 15px;">
      <p>Enter the 6-digit code sent to your phone:</p>
      <input type="text" id="otp-code" placeholder="123456" />
      <button onclick="verifyOTP()" style="background-color: #2E7D32;">Verify & Login</button>
    </div>
  </div>

  <div id="main-app">
    <button class="admin-btn" onclick="openAdmin()">⚙️ Admin</button>

    <div id="admin-panel">
      <h2>🛠️ Admin Dashboard</h2>
      <button onclick="adminAddItem()">➕ Add New Item</button>
    </div>

    <h1>🍔 BurgerBites Restaurant</h1>
    
    <div class="menu-container" id="menu-container">
      <p>Loading menu...</p>
    </div>

    <div class="cart-section" id="cart-section">
      <h2>🛒 Your Current Order</h2>
      <ul id="order-list"><li><em>No items added yet.</em></li></ul>
      <h3>Total: ₹<span id="total-price">0</span></h3>
      <h3>📍 Delivery Address</h3>
      <textarea id="customer-address" rows="3" placeholder="Enter full address..."></textarea><br>
      <button onclick="showPaymentScreen()">Proceed to Payment ➡️</button>
    </div>

    <div id="payment-screen">
      <h2 style="color: #e65100;">💳 Payment Method</h2>
      <p>We currently only accept Cash on Delivery.</p>
      <button onclick="processCOD()" style="background-color: #2E7D32; font-size: 1.3em; padding: 15px;">💵 Cash on Delivery (Confirm)</button>
      <button onclick="goBackToCart()" style="background-color: #757575;">⬅️ Back</button>
    </div>

    <div id="success-screen">
      <h2 style="color: #2E7D32; font-size: 2em;">🎉 Order Created!</h2>
      <p style="font-weight: bold; color: #d32f2f;">Press "Send" in WhatsApp to confirm your food!</p>
      <div>
        <a href="tel:+916000870839" class="contact-btn call-btn">📞 Call Us</a>
        <a href="https://wa.me/916000870839" class="contact-btn msg-btn">💬 Message Us</a>
      </div>
      <button onclick="goBackToMenu()" style="background-color: #333;">⬅️ Back to Menu</button>
    </div>
  </div>

  <script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.0/firebase-app.js";
    import { getFirestore, collection, addDoc, onSnapshot, doc, deleteDoc } from "https://www.gstatic.com/firebasejs/10.12.0/firebase-firestore.js";
    // NEW AUTH IMPORTS:
    import { getAuth, signInWithPopup, GoogleAuthProvider, RecaptchaVerifier, signInWithPhoneNumber, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/10.12.0/firebase-auth.js";

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
    const auth = getAuth(app);

    // --- NEW: WATCH FOR LOGIN STATUS ---
    onAuthStateChanged(auth, (user) => {
      if (user) {
        // Customer is logged in! Hide login screen, show food.
        document.getElementById('login-screen').style.display = "none";
        document.getElementById('main-app').style.display = "block";
      } else {
        // Customer is logged out. Show login screen.
        document.getElementById('login-screen').style.display = "block";
        document.getElementById('main-app').style.display = "none";
      }
    });

    // --- NEW: GOOGLE SIGN IN ---
    window.loginWithGoogle = function() {
      const provider = new GoogleAuthProvider();
      signInWithPopup(auth, provider).catch((error) => alert("Login Failed: " + error.message));
    };

    // --- NEW: PHONE LOGIN WITH RECAPTCHA ---
    window.sendOTP = function() {
      let phoneNumber = document.getElementById('phone-number').value.trim();
      // Add India country code if they forgot it
      if (!phoneNumber.startsWith('+')) { phoneNumber = '+91' + phoneNumber; }

      // Setup the Official Google reCAPTCHA
      if (!window.recaptchaVerifier) {
        window.recaptchaVerifier = new RecaptchaVerifier(auth, 'recaptcha-container', {
          'size': 'normal' // This shows the "I am not a robot" checkbox
        });
      }

      // Send the SMS
      signInWithPhoneNumber(auth, phoneNumber, window.recaptchaVerifier)
        .then((confirmationResult) => {
          window.confirmationResult = confirmationResult;
          document.getElementById('otp-section').style.display = "block";
          document.getElementById('recaptcha-container').style.display = "none"; // Hide recaptcha after success
          alert("Verification code sent to your phone!");
        }).catch((error) => {
          alert("Error: " + error.message);
        });
    };

    window.verifyOTP = function() {
      const code = document.getElementById('otp-code').value.trim();
      window.confirmationResult.confirm(code).catch((error) => {
        alert("Incorrect code! Try again.");
      });
    };

    // --- EXISTING APP LOGIC ---
    const menuContainer = document.getElementById('menu-container');
    
    onSnapshot(collection(db, "menuItems"), (snapshot) => {
      menuContainer.innerHTML = ""; 
      if (snapshot.empty) menuContainer.innerHTML = "<p>Menu is empty!</p>";
      snapshot.forEach((firestoreDoc) => {
        let item = firestoreDoc.data();
        let docId = firestoreDoc.id; 
        let newDiv = document.createElement("div");
        newDiv.className = "menu-item";
        newDiv.innerHTML = `
          <div><h2>${item.name}</h2><p class="price">₹${item.price}</p></div>
          <div><button onclick="addToOrder('${item.name}', ${item.price})">Add to Order</button>
          <button class="delete-btn" onclick="deleteItem('${docId}', '${item.name}')">🗑️ Delete</button></div>`;
        menuContainer.appendChild(newDiv);
      });
    });

    let orderItems = []; let orderTotal = 0;

    window.addToOrder = function(name, price) {
      orderItems.push(name); orderTotal += price; window.updateCartDisplay();
    };

    window.updateCartDisplay = function() {
      const list = document.getElementById('order-list'); list.innerHTML = ""; 
      for (let i = 0; i < orderItems.length; i++) {
        let li = document.createElement('li'); li.innerText = orderItems[i]; list.appendChild(li);
      }
      document.getElementById('total-price').innerText = orderTotal;
    };

    window.showPaymentScreen = function() {
      if (orderItems.length === 0) return alert("Add food first!");
      if (document.getElementById('customer-address').value.trim() === "") return alert("Enter address!");
      document.getElementById('menu-container').style.display = "none";
      document.getElementById('cart-section').style.display = "none";
      document.getElementById('payment-screen').style.display = "block";
    };

    window.processCOD = function() {
      const address = document.getElementById('customer-address').value.trim();
      let msg = "🍔 NEW ORDER 🍔\n\n";
      for (let i = 0; i < orderItems.length; i++) msg += "- " + orderItems[i] + "\n";
      msg += "\n💰 Total: ₹" + orderTotal + "\n💳 Mode: COD\n📍 Address:\n" + address;
      
      window.open("https://wa.me/916000870839?text=" + encodeURIComponent(msg), '_blank');
      
      document.getElementById('payment-screen').style.display = "none";
      document.getElementById('success-screen').style.display = "block";
      
      orderItems = []; orderTotal = 0;
      document.getElementById('order-list').innerHTML = "<li><em>No items added yet.</em></li>";
      document.getElementById('total-price').innerText = "0";
      document.getElementById('customer-address').value = "";
    };

    window.goBackToCart = function() {
      document.getElementById('payment-screen').style.display = "none";
      document.getElementById('menu-container').style.display = "flex";
      document.getElementById('cart-section').style.display = "inline-block";
    };

    window.goBackToMenu = function() {
      document.getElementById('success-screen').style.display = "none";
      document.getElementById('menu-container').style.display = "flex";
      document.getElementById('cart-section').style.display = "inline-block";
    };

    const ADMIN_PIN = "3645"; 
    window.openAdmin = function() {
      if (prompt("Admin PIN:") === ADMIN_PIN) {
        document.getElementById('admin-panel').style.display = "block";
        document.body.classList.add('admin-mode'); 
      } else alert("Denied.");
    };

    window.adminAddItem = async function() {
      let name = prompt("Food name:"); if (!name) return; 
      let price = prompt("Price (Numbers only):"); if (!price || isNaN(price)) return alert("Invalid price");
      try { await addDoc(collection(db, "menuItems"), { name: name, price: parseInt(price) }); } 
      catch (e) { alert("Error: " + e.message); }
    };

    window.deleteItem = async function(docId, name) {
      if (confirm("Delete " + name + "?")) {
        try { await deleteDoc(doc(db, "menuItems", docId)); } 
        catch (e) { alert("Error: " + e.message); }
      }
    };
  </script>

</body>
</html>
