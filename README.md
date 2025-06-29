# Bazaar-Com
<html lang="bn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>বাজার.কম</title>
    <style>
        body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; margin: 0; padding: 0; background: orchid; }
        header, footer { background: #007b5e; color: white; text-align: center; padding: 1rem; }
        nav a { margin: 0 10px; color: #fff; text-decoration: none; font-weight: bold; }
        section { padding: 1rem; max-width: 800px; margin: auto; }
        .hidden { display: none; }
        input, button, select { padding: 10px; margin: 5px 0; width: 100%; border: 1px solid #ccc; border-radius: 5px; }
        button { background-color: #28a745; color: white; cursor: pointer; }
        button:hover { background-color: #218838; }
        .card { background: white; padding: 1rem; margin: 1rem 0; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1); }
        table { width: 100%; border-collapse: collapse; margin-top: 1rem; }
        th, td { border: 1px solid #ccc; padding: 8px; text-align: left; }
        th { background-color: #007b5e; color: white; }
    </style>
</head>
<body>

<header>
    <h1>🌾 বাজার.কম 🌾</h1>
    <nav>
        <a href="#" onclick="showPage('home')">হোম</a>
        <a href="#" onclick="showPage('products')">পণ্য</a>
        <a href="#" onclick="showPage('cart')">কার্ট</a>
        <a href="#" onclick="showPage('orders')">অর্ডার</a>
        <a href="#" onclick="showPage('profile')">প্রোফাইল</a>
        <a href="#" onclick="logout()">লগআউট</a>
    </nav>
</header>

<section id="signup">
    <h2>🔑 সাইন আপ</h2>
    <input type="text" id="signupName" placeholder="নাম">
    <input type="email" id="signupEmail" placeholder="ইমেইল">
    <input type="password" id="signupPassword" placeholder="পাসওয়ার্ড">
    <button onclick="signup()">সাইন আপ করুন</button>
    <p>অ্যাকাউন্ট আছে? <a href="#" onclick="showPage('login')">লগইন করুন</a></p>
</section>

<section id="login" class="hidden">
    <h2>🔐 লগইন</h2>
    <input type="email" id="loginEmail" placeholder="ইমেইল">
    <input type="password" id="loginPassword" placeholder="পাসওয়ার্ড">
    <button onclick="login()">লগইন করুন</button>
    <p>নতুন ইউজার? <a href="#" onclick="showPage('signup')">সাইন আপ করুন</a></p>
</section>

<section id="home" class="hidden">
    <h2>🏠 স্বাগতম বাজার.কম এ!</h2>
    <p>আপনার অনলাইন বাজারের সেরা ঠিকানা।</p>
</section>

<section id="products" class="hidden">
    <h2>🛒 আমাদের পণ্যসমূহ</h2>
    <input type="text" id="searchBox" placeholder="পণ্য অনুসন্ধান করুন..." onkeyup="searchProducts()">
    <div id="productList"></div>
</section>

<section id="cart" class="hidden">
    <h2>🛍️ আপনার কার্ট</h2>
    <div id="cartList"></div>
    <button onclick="placeOrder()">✅ অর্ডার করুন</button>
    <button onclick="downloadCSV()">⬇️ CSV ডাউনলোড</button>
</section>

<section id="orders" class="hidden">
    <h2>📦 আপনার অর্ডারসমূহ</h2>
    <div id="orderList"></div>
</section>

<section id="profile" class="hidden">
    <h2>👤 প্রোফাইল</h2>
    <div id="profileInfo"></div>
</section>

<footer>
    <p>&copy; 2025 বাজার.কম | সর্বস্বত্ব সংরক্ষিত</p>
</footer>

<script>
let products = [
    { name: 'চাল', price: 50 },
    { name: 'ডাল', price: 120 },
    { name: 'তেল', price: 180 },
    { name: 'সবজি', price: 80 },
    { name: 'আটা', price: 60 },
    { name: 'চিনি', price: 70 }
];
let cart = JSON.parse(localStorage.getItem('cart') || '[]');

function signup() {
    const name = document.getElementById('signupName').value;
    const email = document.getElementById('signupEmail').value;
    const pass = document.getElementById('signupPassword').value;
    if (name && email && pass) {
        localStorage.setItem('user', JSON.stringify({ name, email, pass }));
        alert('সাইনআপ সফল! এখন লগইন করুন।');
        showPage('login');
    } else {
        alert('সব ঘর পূরণ করুন।');
    }
}

function login() {
    const email = document.getElementById('loginEmail').value;
    const pass = document.getElementById('loginPassword').value;
    const user = JSON.parse(localStorage.getItem('user'));
    if (user && user.email === email && user.pass === pass) {
        localStorage.setItem('loggedIn', 'true');
        alert('লগইন সফল!');
        showPage('home');
        loadProfile();
    } else {
        alert('ভুল ইমেইল বা পাসওয়ার্ড!');
    }
}

function logout() {
    localStorage.removeItem('loggedIn');
    alert('লগআউট সম্পন্ন।');
    showPage('login');
}

function showPage(pageId) {
    document.querySelectorAll('section').forEach(s => s.classList.add('hidden'));
    document.getElementById(pageId).classList.remove('hidden');
    if (pageId === 'products') loadProducts();
    if (pageId === 'cart') loadCart();
    if (pageId === 'orders') loadOrders();
}

function loadProfile() {
    const user = JSON.parse(localStorage.getItem('user'));
    document.getElementById('profileInfo').innerHTML = `<p>নাম: ${user.name}</p><p>ইমেইল: ${user.email}</p>`;
}

function loadProducts(filter = '') {
    const list = document.getElementById('productList');
    list.innerHTML = '';
    products.filter(p => p.name.includes(filter)).forEach((p, index) => {
        list.innerHTML += `<div class="card"><h4>${p.name}</h4><p>দাম: ${p.price} টাকা</p><button onclick="addToCart(${index})">কার্টে যোগ করুন</button></div>`;
    });
}

function searchProducts() {
    const query = document.getElementById('searchBox').value;
    loadProducts(query);
}

function addToCart(index) {
    cart.push(products[index]);
    localStorage.setItem('cart', JSON.stringify(cart));
    alert('পণ্য কার্টে যোগ হয়েছে।');
    loadCart();
}

function loadCart() {
    const list = document.getElementById('cartList');
    list.innerHTML = '';
    cart = JSON.parse(localStorage.getItem('cart') || '[]');
    if (cart.length === 0) {
        list.innerHTML = '<p>কার্ট খালি।</p>';
    } else {
        list.innerHTML = '<table><tr><th>পণ্য</th><th>দাম</th></tr>' +
            cart.map(item => `<tr><td>${item.name}</td><td>${item.price}</td></tr>`).join('') + '</table>';
    }
}

function placeOrder() {
    if (cart.length > 0) {
        const orders = JSON.parse(localStorage.getItem('orders') || '[]');
        orders.push(`অর্ডার: ${cart.map(i => i.name).join(', ')}`);
        localStorage.setItem('orders', JSON.stringify(orders));
        localStorage.removeItem('cart');
        cart = [];
        alert('অর্ডার সম্পন্ন হয়েছে।');
        loadCart();
    } else {
        alert('কার্ট খালি!');
    }
}

function loadOrders() {
    const list = document.getElementById('orderList');
    const orders = JSON.parse(localStorage.getItem('orders') || '[]');
    list.innerHTML = orders.length ? orders.map(o => `<p>${o}</p>`).join('') : '<p>কোন অর্ডার নেই।</p>';
}

function downloadCSV() {
    let csv = 'পণ্য,দাম\n';
    cart.forEach(item => {
        csv += `${item.name},${item.price}\n`;
    });
    const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' });
    const link = document.createElement('a');
    link.setAttribute('href', URL.createObjectURL(blob));
    link.setAttribute('download', 'cart.csv');
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
}

window.onload = () => {
    if (localStorage.getItem('loggedIn') === 'true') {
        showPage('home');
        loadProfile();
    } else {
        showPage('signup');
    }
};
</script>

</body>
</html>
