# Pharma.Hisab-
<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Pharma Hisab - د عمده فروشی مدیریت سیستم</title>
    <!-- Tailwind CSS for modern responsive design -->
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
    <!-- Google Fonts: Vazirmatn for professional Persian/Pashto typography -->
    <link href="https://fonts.googleapis.com/css2?family=Vazirmatn:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Vazirmatn', sans-serif; }
        @media print {
            body * { visibility: hidden; }
            #printable-invoice, #printable-invoice * { visibility: visible; }
            #printable-invoice { position: absolute; left: 0; top: 0; width: 100%; }
        }
    </style>
</head>
<body class="bg-gray-100 text-gray-800 min-h-screen flex flex-col">

    <!-- Top Navigation Bar -->
    <header class="bg-indigo-700 text-white shadow-md">
        <div class="container mx-auto px-4 py-3 flex justify-between items-center">
            <div class="flex items-center space-x-3 space-x-reverse">
                <span class="text-2xl font-bold bg-white text-indigo-700 px-3 py-1 rounded-lg shadow">Pharma Hisab</span>
                <span class="text-sm opacity-95">د عمده فروشی مسلکي سیستم | Daftani Pharma</span>
            </div>
            <div class="flex items-center space-x-4 space-x-reverse text-sm">
                <span id="current-date" class="bg-indigo-800 px-3 py-1 rounded"></span>
                <button onclick="switchTab('pos')" class="bg-indigo-600 hover:bg-indigo-500 px-3 py-1.5 rounded transition">فکتور (POS)</button>
                <button onclick="switchTab('inventory')" class="bg-indigo-600 hover:bg-indigo-500 px-3 py-1.5 rounded transition">انبار او پرچیس</button>
                <button onclick="switchTab('ledger')" class="bg-indigo-600 hover:bg-indigo-500 px-3 py-1.5 rounded transition">مشتریان او لېجر</button>
                <button onclick="switchTab('reports')" class="bg-indigo-600 hover:bg-indigo-500 px-3 py-1.5 rounded transition">راپورونه</button>
            </div>
        </div>
    </header>

    <!-- Main Container -->
    <main class="container mx-auto px-4 py-6 flex-grow">

        <!-- 1. POS & INVOICE TAB -->
        <section id="tab-pos" class="tab-content grid grid-cols-1 lg:grid-cols-3 gap-6">
            <!-- Left: Product Search & List -->
            <div class="lg:col-span-2 bg-white p-4 rounded-xl shadow-md">
                <div class="flex justify-between items-center mb-4">
                    <h2 class="text-lg font-bold text-indigo-700">د دواګانو لټون او پلور</h2>
                    <input type="text" id="pos-search" oninput="filterPOSProducts()" placeholder="دوا دلته ولټوئ..." class="border rounded-lg px-3 py-1.5 text-sm w-64 focus:outline-none focus:ring-2 focus:ring-indigo-500">
                </div>
                <div class="overflow-x-auto max-h-96 overflow-y-auto">
                    <table class="w-full text-right text-sm">
                        <thead class="bg-gray-50 text-gray-700 sticky top-0">
                            <tr>
                                <th class="p-2">دوا نوم</th>
                                <th class="p-2">موجودي</th>
                                <th class="p-2">قیمت</th>
                                <th class="p-2">اسعار</th>
                                <th class="p-2 text-center">عملیه</th>
                            </tr>
                        </thead>
                        <tbody id="pos-products-table" class="divide-y divide-gray-100">
                            <!-- Populated by JS -->
                        </tbody>
                    </table>
                </div>
            </div>

            <!-- Right: Cart & Checkout -->
            <div class="bg-white p-4 rounded-xl shadow-md flex flex-col justify-between">
                <div>
                    <h2 class="text-lg font-bold text-indigo-700 mb-3 border-b pb-2">سوداګریز فکتور (Cart)</h2>
                    <div class="mb-3">
                        <label class="block text-xs text-gray-600 mb-1">د مشتری / دواخنې نوم:</label>
                        <select id="invoice-customer" onchange="updateCustomerDebtView()" class="w-full border rounded-lg px-3 py-1.5 text-sm focus:ring-2 focus:ring-indigo-500">
                            <option value="">مشتری وټاکئ...</option>
                        </select>
                        <div id="customer-debt-alert" class="text-xs text-red-600 mt-1 font-medium"></div>
                    </div>
                    
                    <div class="max-h-48 overflow-y-auto mb-3 divide-y divide-gray-100" id="cart-items-container">
                        <!-- Cart items -->
                    </div>
                </div>

                <div class="border-t pt-3 space-y-2">
                    <div class="flex justify-between text-sm">
                        <span>مجموعه:</span>
                        <span id="cart-subtotal" class="font-bold">0.00</span>
                    </div>
                    <div class="flex justify-between items-center text-sm">
                        <span>تخفیف:</span>
                        <input type="number" id="invoice-discount" value="0" oninput="calculateCartTotal()" class="border rounded px-2 py-1 w-24 text-left text-sm">
                    </div>
                    <div class="flex justify-between text-base font-bold text-indigo-700 border-t pt-2">
                        <span>پاتې مجموعي رقم:</span>
                        <span id="cart-grand-total">0.00</span>
                    </div>
                    <div class="flex space-x-2 space-x-reverse pt-2">
                        <button onclick="completeSale()" class="flex-1 bg-green-600 hover:bg-green-500 text-white py-2 rounded-lg font-medium transition text-sm">پلور بشپړول او پرېنټ</button>
                        <button onclick="clearCart()" class="bg-gray-300 hover:bg-gray-400 text-gray-700 px-3 py-2 rounded-lg text-sm transition">خالي کول</button>
                    </div>
                </div>
            </div>
        </section>

        <!-- 2. INVENTORY & PURCHASE TAB -->
        <section id="tab-inventory" class="tab-content hidden space-y-6">
            <div class="bg-white p-6 rounded-xl shadow-md">
                <h2 class="text-xl font-bold text-indigo-700 mb-4">نوی پرچیس (دواګانو اخیستل او انبار ته اضافه کول)</h2>
                <form id="purchase-form" onsubmit="handlePurchaseSubmit(event)" class="grid grid-cols-1 md:grid-cols-4 gap-4">
                    <div>
                        <label class="block text-sm font-medium mb-1">دوا نوم:</label>
                        <input type="text" id="p-name" required class="w-full border rounded-lg px-3 py-2 text-sm focus:ring-2 focus:ring-indigo-500">
                    </div>
                    <div>
                        <label class="block text-sm font-medium mb-1">شرکت / عرضه کونکی:</label>
                        <input type="text" id="p-supplier" required class="w-full border rounded-lg px-3 py-2 text-sm focus:ring-2 focus:ring-indigo-500">
                    </div>
                    <div>
                        <label class="block text-sm font-medium mb-1">تعداد (کارتن/بکس):</label>
                        <input type="number" id="p-qty" required min="1" class="w-full border rounded-lg px-3 py-2 text-sm focus:ring-2 focus:ring-indigo-500">
                    </div>
                    <div>
                        <label class="block text-sm font-medium mb-1">د اخیستلو قیمت (Purchase Price):</label>
                        <input type="number" id="p-buy-price" required step="0.01" class="w-full border rounded-lg px-3 py-2 text-sm focus:ring-2 focus:ring-indigo-500">
                    </div>
                    <div>
                        <label class="block text-sm font-medium mb-1">د پلور قیمت (Sell Price):</label>
                        <input type="number" id="p-sell-price" required step="0.01" class="w-full border rounded-lg px-3 py-2 text-sm focus:ring-2 focus:ring-indigo-500">
                    </div>
                    <div>
                        <label class="block text-sm font-medium mb-1">اسعار:</label>
                        <select id="p-currency" class="w-full border rounded-lg px-3 py-2 text-sm focus:ring-2 focus:ring-indigo-500">
                            <option value="AFN">افغانۍ (AFN)</option>
                            <option value="USD">ډالر (USD)</option>
                            <option value="PKR">کلدارې (PKR)</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-sm font-medium mb-1">د تاریخ ختمېدو موده (Expiry Date):</label>
                        <input type="date" id="p-expiry" required class="w-full border rounded-lg px-3 py-2 text-sm focus:ring-2 focus:ring-indigo-500">
                    </div>
                    <div class="flex items-end">
                        <button type="submit" class="w-full bg-indigo-600 hover:bg-indigo-500 text-white py-2 rounded-lg font-medium transition text-sm">انبار ته اضافه کول</button>
                    </div>
                </form>
            </div>

            <div class="bg-white p-6 rounded-xl shadow-md">
                <div class="flex justify-between items-center mb-4">
                    <h2 class="text-lg font-bold text-indigo-700">د انبار او دواګانو موجودي لست</h2>
                    <input type="text" id="inventory-search" oninput="renderInventory()" placeholder="د انبار لټون..." class="border rounded-lg px-3 py-1.5 text-sm w-64 focus:ring-2 focus:ring-indigo-500">
                </div>
                <div class="overflow-x-auto">
                    <table class="w-full text-right text-sm">
                        <thead class="bg-gray-50 text-gray-700">
                            <tr>
                                <th class="p-3">دوا نوم</th>
                                <th class="p-3">شرکت</th>
                                <th class="p-3">موجودي</th>
                                <th class="p-3">پرچیس قیمت</th>
                                <th class="p-3">پلور قیمت</th>
                                <th class="p-3">اسعار</th>
                                <th class="p-3">تاريخ ختمېدل</th>
                            </tr>
                        </thead>
                        <tbody id="inventory-table-body" class="divide-y divide-gray-100">
                            <!-- Populated by JS -->
                        </tbody>
                    </table>
                </div>
            </div>
        </section>

        <!-- 3. CUSTOMERS & LEDGER TAB -->
        <section id="tab-ledger" class="tab-content hidden space-y-6">
            <div class="bg-white p-6 rounded-xl shadow-md">
                <h2 class="text-xl font-bold text-indigo-700 mb-4">نوی مشتری ثبتول</h2>
                <form onsubmit="handleCustomerSubmit(event)" class="grid grid-cols-1 md:grid-cols-3 gap-4">
                    <div>
                        <label class="block text-sm font-medium mb-1">مشتری / دواخنه نوم:</label>
                        <input type="text" id="c-name" required class="w-full border rounded-lg px-3 py-2 text-sm">
                    </div>
                    <div>
                        <label class="block text-sm font-medium mb-1">تلفن نمبر:</label>
                        <input type="text" id="c-phone" required class="w-full border rounded-lg px-3 py-2 text-sm">
                    </div>
                    <div class="flex items-end">
                        <button type="submit" class="w-full bg-indigo-600 hover:bg-indigo-500 text-white py-2 rounded-lg font-medium transition text-sm">مشتری ثبتول</button>
                    </div>
                </form>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                <div class="bg-white p-6 rounded-xl shadow-md">
                    <h2 class="text-lg font-bold text-indigo-700 mb-3">د مشتریانو لست او باقیدارۍ</h2>
                    <div class="overflow-x-auto max-h-80 overflow-y-auto">
                        <table class="w-full text-right text-sm">
                            <thead class="bg-gray-50">
                               <tr><th class="p-2">نوم</th><th class="p-2">تلفن</th><th class="p-2">پاتې پور</th><th class="p-2">عملیه</th></tr>
                            </thead>
                            <tbody id="customers-table-body" class="divide-y divide-gray-100"></tbody>
                        </table>
                    </div>
                </div>

                <div class="bg-white p-6 rounded-xl shadow-md">
                    <h2 class="text-lg font-bold text-indigo-700 mb-3">د پور تحویلي (تصفیه)</h2>
                    <form onsubmit="handlePaymentSubmit(event)" class="space-y-4">
                        <div>
                            <label class="block text-sm font-medium mb-1">مشتری وټاکئ:</label>
                            <select id="pay-customer" class="w-full border rounded-lg px-3 py-2 text-sm"></select>
                        </div>
                        <div>
                            <label class="block text-sm font-medium mb-1">د ورکړل شویو پیسو اندازه:</label>
                            <input type="number" id="pay-amount" required step="0.01" class="w-full border rounded-lg px-3 py-2 text-sm">
                        </div>
                        <button type="submit" class="w-full bg-green-600 hover:bg-green-500 text-white py-2 rounded-lg font-medium transition text-sm">پور تصفیه او ثبتول</button>
                    </form>
                </div>
            </div>
        </section>

        <!-- 4. REPORTS TAB -->
        <section id="tab-reports" class="tab-content hidden space-y-6">
            <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                <div class="bg-white p-5 rounded-xl shadow-md border-r-4 border-indigo-600">
                    <h3 class="text-sm font-medium text-gray-500">د بازار ټول پورونه (Total Debt)</h3>
                    <p id="stat-total-debt" class="text-2xl font-bold text-indigo-700 mt-2">0.00 AFN</p>
                </div>
                <div class="bg-white p-5 rounded-xl shadow-md border-r-4 border-green-600">
                    <h3 class="text-sm font-medium text-gray-500">د ننني پلور مجموعه</h3>
                    <p id="stat-today-sales" class="text-2xl font-bold text-green-700 mt-2">0.00</p>
                </div>
                <div class="bg-white p-5 rounded-xl shadow-md border-r-4 border-blue-600">
                    <h3 class="text-sm font-medium text-gray-500">د انبار مجموعي ارزښت</h3>
                    <p id="stat-inventory-val" class="text-2xl font-bold text-blue-700 mt-2">0.00</p>
                </div>
            </div>
        </section>

    </main>

    <!-- Hidden Printable Area for Invoice -->
    <div id="printable-invoice" class="hidden p-8 bg-white text-right">
        <div class="text-center mb-6">
            <h1 class="text-2xl font-bold">Daftani Pharma Wholesale</h1>
            <p class="text-sm text-gray-600">پته: دورازه بلخ مزار هتل | اړیکه: 0782665516</p>
            <hr class="my-3">
        </div>
        <div class="flex justify-between mb-4 text-sm">
            <div><span class="font-bold">مشتری:</span> <span id="print-cust-name"></span></div>
            <div><span class="font-bold">نېټه:</span> <span id="print-date"></span></div>
        </div>
        <table class="w-full border-collapse border border-gray-300 text-sm mb-4">
            <thead>
                <tr class="bg-gray-100">
                    <th class="border border-gray-300 p-2">دوا نوم</th>
                    <th class="border border-gray-300 p-2">تعداد</th>
                    <th class="border border-gray-300 p-2">قیمت</th>
                    <th class="border border-gray-300 p-2">مجموعه</th>
                </tr>
            </thead>
            <tbody id="print-items-body"></tbody>
        </table>
        <div class="text-left space-y-1 text-sm font-bold">
            <p>تخفیف: <span id="print-discount">0</span></p>
            <p class="text-base text-indigo-700">پاتې مجموعي رقم: <span id="print-grand-total">0</span></p>
        </div>
    </div>

    <!-- Application JavaScript Logic -->
    <script>
        // State Management
        let inventory = JSON.parse(localStorage.getItem('ph_inventory')) || [
            { id: 1, name: 'Panadol Extra', supplier: 'Alokozay Bros', qty: 150, buyPrice: 40, sellPrice: 50, currency: 'AFN', expiry: '2027-12-31' },
            { id: 2, name: 'Amoxicillin 500mg', supplier: 'Samar Pharma', qty: 80, buyPrice: 80, sellPrice: 100, currency: 'AFN', expiry: '2028-06-30' }
        ];

        let customers = JSON.parse(localStorage.getItem('ph_customers')) || [
            { id: 1, name: 'مصطفی عمر درملتون', phone: '0790193710', debt: 1500 },
            { id: 2, name: 'غریب زاده درملتون', phone: '0787354753', debt: 0 }
        ];

        let cart = [];

        document.getElementById('current-date').innerText = new Date().toLocaleDateString('fa-IR');

        function saveData() {
            localStorage.setItem('ph_inventory', JSON.stringify(inventory));
            localStorage.setItem('ph_customers', JSON.stringify(customers));
        }

        function switchTab(tabId) {
            document.querySelectorAll('.tab-content').forEach(el => el.classList.add('hidden'));
            document.getElementById('tab-' + tabId).classList.remove('hidden');
            if(tabId === 'pos') renderPOSProducts();
            if(tabId === 'inventory') renderInventory();
            if(tabId === 'ledger') renderCustomers();
            if(tabId === 'reports') updateReports();
        }

        // POS Functions
        function renderPOSProducts() {
            const query = document.getElementById('pos-search').value.toLowerCase();
            const tbody = document.getElementById('pos-products-table');
            tbody.innerHTML = '';
            inventory.filter(i => i.name.toLowerCase().includes(query)).forEach(item => {
                tbody.innerHTML += `
                    <tr class="hover:bg-gray-50">
                        <td class="p-2 font-medium">${item.name}</td>
                        <td class="p-2">${item.qty}</td>
                        <td class="p-2">${item.sellPrice}</td>
                        <td class="p-2">${item.currency}</td>
                        <td class="p-2 text-center">
                            <button onclick="addToCart(${item.id})" class="bg-indigo-600 hover:bg-indigo-500 text-white px-2.5 py-1 rounded text-xs transition">اضافه کول</button>
                        </td>
                    </tr>
                `;
            });
            updateCustomerDropdowns();
        }

        function addToCart(id) {
            const product = inventory.find(i => i.id === id);
            if (!product || product.qty <= 0) return alert('دوا نشته یا خلاصې شوې ده!');
            let cartItem = cart.find(c => c.id === id);
            if (cartItem) {
                if (cartItem.qty < product.qty) cartItem.qty++;
                else alert('نور موجودي نشته!');
            } else {
                cart.push({ ...product, qty: 1 });
            }
            renderCart();
        }

        function renderCart() {
            const container = document.getElementById('cart-items-container');
            container.innerHTML = '';
            let subtotal = 0;
            cart.forEach((item, index) => {
                subtotal += item.sellPrice * item.qty;
                container.innerHTML += `
                    <div class="flex justify-between items-center py-2 text-sm border-b">
                        <div>
                            <p class="font-medium">${item.name}</p>
                            <p class="text-xs text-gray-500">${item.qty} × ${item.sellPrice} ${item.currency}</p>
                        </div>
                        <button onclick="cart.splice(${index}, 1); renderCart();" class="text-red-500 hover:text-red-700 text-xs">لیرې کول</button>
                    </div>
                `;
            });
            document.getElementById('cart-subtotal').innerText = subtotal.toFixed(2);
            calculateCartTotal();
        }

        function calculateCartTotal() {
            let subtotal = cart.reduce((sum, item) => sum + (item.sellPrice * item.qty), 0);
            let discount = parseFloat(document.getElementById('invoice-discount').value) || 0;
            let grandTotal = Math.max(0, subtotal - discount);
            document.getElementById('cart-grand-total').innerText = grandTotal.toFixed(2);
        }

        function updateCustomerDropdowns() {
            ['invoice-customer', 'pay-customer'].forEach(id => {
                const select = document.getElementById(id);
                if(!select) return;
                const currentVal = select.value;
                select.innerHTML = id === 'invoice-customer' ? '<option value="">مشتری وټاکئ...</option>' : '';
                customers.forEach(c => {
                    select.innerHTML += `<option value="${c.id}">${c.name} (${c.phone})</option>`;
                });
                select.value = currentVal;
            });
        }

        function updateCustomerDebtView() {
            const custId = document.getElementById('invoice-customer').value;
            const alertBox = document.getElementById('customer-debt-alert');
            if(!custId) { alertBox.innerText = ''; return; }
            const cust = customers.find(c => c.id == custId);
            alertBox.innerText = cust && cust.debt > 0 ? `پخوانی پور (باقیداری): ${cust.debt} AFN` : '';
        }

        function clearCart() {
            cart = [];
            document.getElementById('invoice-discount').value = 0;
            renderCart();
        }

        function completeSale() {
            if(cart.length === 0) return alert('کارټ خالي دی!');
            const custId = document.getElementById('invoice-customer').value;
            if(!custId) return alert('لطفاً مشتری وټاکئ!');
            
            // Deduct stock
            cart.forEach(cItem => {
                let invItem = inventory.find(i => i.id === cItem.id);
                if(invItem) invItem.qty -= cItem.qty;
            });

            let subtotal = cart.reduce((sum, item) => sum + (item.sellPrice * item.qty), 0);
            let discount = parseFloat(document.getElementById('invoice-discount').value) || 0;
            let grandTotal = subtotal - discount;

            // Add debt if not paid in full (Simplified wholesale credit rule)
            let cust = customers.find(c => c.id == custId);
            if(cust) cust.debt += grandTotal;

            saveData();
            alert('پلور په بریالیتوب سره ثبت شو!');
            clearCart();
            renderPOSProducts();
        }

        // Inventory & Purchase Functions
        function handlePurchaseSubmit(e) {
            e.preventDefault();
            const newItem = {
                id: Date.now(),
                name: document.getElementById('p-name').value,
                supplier: document.getElementById('p-supplier').value,
                qty: parseInt(document.getElementById('p-qty').value),
                buyPrice: parseFloat(document.getElementById('p-buy-price').value),
                sellPrice: parseFloat(document.getElementById('p-sell-price').value),
                currency: document.getElementById('p-currency').value,
                expiry: document.getElementById('p-expiry').value
            };
            inventory.push(newItem);
            saveData();
            e.target.reset();
            renderInventory();
            alert('نوی پرچیس په بریالیتوب سره په انبار کې ثبت شو!');
        }

        function renderInventory() {
            const query = document.getElementById('inventory-search').value.toLowerCase();
            const tbody = document.getElementById('inventory-table-body');
            tbody.innerHTML = '';
            inventory.filter(i => i.name.toLowerCase().includes(query) || i.supplier.toLowerCase().includes(query)).forEach(item => {
                tbody.innerHTML += `
                    <tr class="hover:bg-gray-50">
                        <td class="p-3 font-medium">${item.name}</td>
                        <td class="p-3">${item.supplier}</td>
                        <td class="p-3">${item.qty}</td>
                        <td class="p-3">${item.buyPrice}</td>
                        <td class="p-3">${item.sellPrice}</td>
                        <td class="p-3">${item.currency}</td>
                        <td class="p-3">${item.expiry}</td>
                    </tr>
                `;
            });
        }

        // Customer & Ledger Functions
        function handleCustomerSubmit(e) {
            e.preventDefault();
            const newCust = {
                id: Date.now(),
                name: document.getElementById('c-name').value,
                phone: document.getElementById('c-phone').value,
                debt: 0
            };
            customers.push(newCust);
            saveData();
            e.target.reset();
            renderCustomers();
            alert('مشتری ثبت شو!');
        }

        function renderCustomers() {
            const tbody = document.getElementById('customers-table-body');
            tbody.innerHTML = '';
            customers.forEach(c => {
                tbody.innerHTML += `
                    <tr class="hover:bg-gray-50">
                        <td class="p-2 font-medium">${c.name}</td>
                        <td class="p-2">${c.phone}</td>
                        <td class="p-2 text-red-600 font-bold">${c.debt} AFN</td>
                        <td class="p-2"><button onclick="viewCustomerLedger(${c.id})" class="text-indigo-600 hover:underline text-xs">لېجر کتل</button></td>
                    </tr>
                `;
            });
            updateCustomerDropdowns();
        }

        function handlePaymentSubmit(e) {
            e.preventDefault();
            const custId = document.getElementById('pay-customer').value;
            const amount = parseFloat(document.getElementById('pay-amount').value);
            let cust = customers.find(c => c.id == custId);
            if(cust) {
                cust.debt = Math.max(0, cust.debt - amount);
                saveData();
                e.target.reset();
                renderCustomers();
                alert('پیسې په بریالیتوب سره تحویل او د مشتری پور کم شو!');
            }
        }

        function viewCustomerLedger(id) {
            const cust = customers.find(c => c.id == id);
            if(cust) alert(`د مشتری (${cust.name}) پاتې باقیداری (پور): ${cust.debt} AFN`);
        }

        function updateReports() {
            let totalDebt = customers.reduce((sum, c) => sum + c.debt, 0);
            let inventoryVal = inventory.reduce((sum, i) => sum + (i.qty * i.buyPrice), 0);
            document.getElementById('stat-total-debt').innerText = totalDebt.toFixed(2) + ' AFN';
            document.getElementById('stat-inventory-val').innerText = inventoryVal.toFixed(2) + ' AFN';
            document.getElementById('stat-today-sales').innerText = '0.00 AFN';
        }

        // Initialize on load
        renderPOSProducts();
    </script>
</body>
</html>
