<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Metal Stock Inventory Management</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Inter', sans-serif; }
        .modal { display: none; }
        .modal.active { display: flex; }
        .hidden-by-auth { display: none !important; }
        #loadingOverlay {
            display: none;
            position: fixed; inset: 0;
            background-color: rgba(0, 0, 0, 0.6);
            z-index: 9999;
            justify-content: center; align-items: center;
        }
        .spinner {
            border: 8px solid #f3f3f3; border-top: 8px solid #3498db;
            border-radius: 50%; width: 60px; height: 60px;
            animation: spin 1s linear infinite;
        }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
    </style>
</head>
<body class="bg-gray-100 text-gray-800">

    <div id="loadingOverlay"><div class="spinner"></div></div>

    <div id="authContainer" class="min-h-screen flex items-center justify-center bg-gray-50 py-12 px-4 sm:px-6 lg:px-8">
        <div class="max-w-md w-full space-y-8 bg-white p-10 rounded-2xl shadow-lg">
            <div><h2 id="authTitle" class="mt-6 text-center text-3xl font-extrabold text-gray-900">Sign in to your account</h2></div>
            <form id="authForm" class="mt-8 space-y-6">
                <div class="rounded-md shadow-sm -space-y-px">
                    <div><label for="email-address" class="sr-only">Email address</label><input id="email-address" name="email" type="email" autocomplete="email" required class="appearance-none rounded-none relative block w-full px-3 py-3 border border-gray-300 placeholder-gray-500 text-gray-900 rounded-t-md focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 focus:z-10 sm:text-sm" placeholder="Email address"></div>
                    <div><label for="password" class="sr-only">Password</label><input id="password" name="password" type="password" autocomplete="current-password" required class="appearance-none rounded-none relative block w-full px-3 py-3 border border-gray-300 placeholder-gray-500 text-gray-900 rounded-b-md focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 focus:z-10 sm:text-sm" placeholder="Password"></div>
                </div>
                <div class="flex items-center justify-end"><div class="text-sm"><a href="#" id="forgotPasswordLink" class="font-medium text-indigo-600 hover:text-indigo-500">Forgot your password?</a></div></div>
                <div>
                    <button id="authSubmitBtn" type="submit" class="group relative w-full flex justify-center py-3 px-4 border border-transparent text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
                        Sign In
                    </button>
                </div>
            </form>
            <p class="mt-2 text-center text-sm text-gray-600"><a href="#" id="authToggleLink" class="font-medium text-indigo-600 hover:text-indigo-500">Don't have an account? Sign Up</a></p>
        </div>
    </div>

    <div id="appContainer" class="hidden-by-auth">
        <nav class="bg-indigo-600 shadow-md"><div class="container mx-auto px-4 sm:px-6 lg:px-8"><div class="flex items-center justify-between h-16"><div class="flex-shrink-0"><h1 class="text-white text-xl font-bold">Inventory</h1></div><div class="flex items-center"><p id="userEmail" class="text-indigo-200 text-sm mr-4"></p><button id="signOutBtn" class="bg-indigo-700 text-white font-semibold py-2 px-4 rounded-lg shadow-md hover:bg-indigo-800 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-offset-indigo-600 focus:ring-white transition">Sign Out</button></div></div></div></nav>
        <div class="container mx-auto p-4 sm:p-6 lg:p-8">
            <div class="bg-white p-6 rounded-2xl shadow-lg mb-8">
                <h2 class="text-2xl font-semibold mb-6 border-b pb-4">Add New Stock Item</h2>
                <form id="addItemForm" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
                    <div><label for="material" class="block text-sm font-medium text-gray-700 mb-1">Material</label><select id="material" name="material" class="w-full p-3 bg-gray-50 border border-gray-300 rounded-lg"><option>Aluminium 6061</option><option>Aluminium 6082</option><option>Aluminium 7075</option><option>Stainless Steel 304</option><option>Stainless Steel 303</option><option>Stainless Steel 420</option><option>Stainless Steel 316</option><option>Stainless Steel 316L</option><option>Stainless Steel 202</option><option>Brass</option><option>Copper</option></select></div>
                    <div><label for="shape" class="block text-sm font-medium text-gray-700 mb-1">Shape</label><select id="shape" name="shape" class="w-full p-3 bg-gray-50 border border-gray-300 rounded-lg"><option>Round Rod</option><option>Hexagon</option><option>Flat</option><option>Plate</option><option>Pipe</option><option>Square</option></select></div>
                    <div><label for="dimensions" class="block text-sm font-medium text-gray-700 mb-1">Dimensions</label><input type="text" id="dimensions" name="dimensions" class="w-full p-3 bg-gray-50 border border-gray-300 rounded-lg" placeholder="e.g., 19.05x63.5 mm"></div>
                    <div><label for="initialStock" class="block text-sm font-medium text-gray-700 mb-1">Initial Quantity (kg/pcs)</label><input type="number" id="initialStock" name="initialStock" class="w-full p-3 bg-gray-50 border border-gray-300 rounded-lg" placeholder="e.g., 100" min="0" step="any"></div>
                    <div class="md:col-span-2 lg:col-span-4 flex justify-end"><button type="submit" class="bg-indigo-600 text-white font-semibold py-3 px-6 rounded-lg shadow-md hover:bg-indigo-700">Add Item</button></div>
                </form>
            </div>
            <div class="bg-white p-6 rounded-2xl shadow-lg"><div class="flex flex-col sm:flex-row justify-between items-center mb-6 border-b pb-4"><h2 class="text-2xl font-semibold mb-4 sm:mb-0">Current Inventory</h2><div class="relative w-full sm:w-72"><div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none"><svg class="h-5 w-5 text-gray-400" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M8 4a4 4 0 100 8 4 4 0 000-8zM2 8a6 6 0 1110.89 3.476l4.817 4.817a1 1 0 01-1.414 1.414l-4.816-4.816A6 6 0 012 8z" clip-rule="evenodd" /></svg></div><input type="text" id="searchInput" class="block w-full pl-10 pr-3 py-2.5 border border-gray-300 rounded-lg" placeholder="Search inventory..."></div></div><div class="overflow-x-auto"><table class="min-w-full divide-y divide-gray-200"><thead class="bg-gray-50"><tr><th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">Material</th><th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">Shape</th><th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">Dimensions</th><th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">Current Stock</th><th class="px-6 py-3 text-center text-xs font-medium text-gray-500 uppercase">Actions</th></tr></thead><tbody id="inventoryTableBody" class="bg-white divide-y divide-gray-200"></tbody></table></div></div>
        </div>
    </div>

    <div id="transactionModal" class="modal fixed inset-0 bg-gray-600 bg-opacity-50 h-full w-full items-center justify-center"><div class="relative mx-auto p-8 border w-full max-w-md shadow-lg rounded-2xl bg-white"><div class="text-center"><h3 class="text-2xl font-bold text-gray-900">Manage Stock</h3><div class="mt-4 px-7 py-3"><p class="text-sm text-gray-500" id="modalItemInfo"></p><form id="transactionForm" class="mt-6 space-y-4"><input type="hidden" id="modalItemId"><div><label for="transactionType" class="block text-sm font-medium text-gray-700 mb-1 text-left">Transaction Type</label><select id="transactionType" class="w-full p-3 bg-gray-50 border border-gray-300 rounded-lg"><option value="inward">Inward (+)</option><option value="outward">Outward (-)</option></select></div><div><label for="transactionAmount" class="block text-sm font-medium text-gray-700 mb-1 text-left">Quantity</label><input type="number" id="transactionAmount" class="w-full p-3 bg-gray-50 border border-gray-300 rounded-lg" placeholder="e.g., 10" min="0" step="any" required></div><div><label for="transactionDate" class="block text-sm font-medium text-gray-700 mb-1 text-left">Date</label><input type="date" id="transactionDate" class="w-full p-3 bg-gray-50 border border-gray-300 rounded-lg" required></div><div id="customerNameContainer" style="display: none;"><label for="customerName" class="block text-sm font-medium text-gray-700 mb-1 text-left">Customer Name</label><input type="text" id="customerName" class="w-full p-3 bg-gray-50 border border-gray-300 rounded-lg" placeholder="e.g., ABC Engineering"></div><div class="flex items-center justify-end pt-4 gap-4"><button id="closeModalBtn" type="button" class="bg-gray-200 text-gray-800 font-semibold py-2 px-6 rounded-lg hover:bg-gray-300">Cancel</button><button type="submit" class="bg-green-500 text-white font-semibold py-2 px-6 rounded-lg shadow-md hover:bg-green-600">Confirm</button></div></form></div></div></div></div>
    <div id="historyModal" class="modal fixed inset-0 bg-gray-600 bg-opacity-50 h-full w-full items-center justify-center p-4"><div class="relative mx-auto p-8 border w-full max-w-4xl h-full max-h-[90vh] shadow-lg rounded-2xl bg-white flex flex-col"><h3 class="text-2xl font-bold text-gray-900 mb-4 text-center">Transaction History</h3><p id="historyItemInfo" class="text-center text-gray-600 mb-6"></p><div class="overflow-y-auto flex-grow"><table class="min-w-full divide-y divide-gray-200"><thead class="bg-gray-50 sticky top-0"><tr><th class="px-4 py-3 text-left text-xs font-medium text-gray-500 uppercase">Date</th><th class="px-4 py-3 text-left text-xs font-medium text-gray-500 uppercase">Type</th><th class="px-4 py-3 text-left text-xs font-medium text-gray-500 uppercase">Amount</th><th class="px-4 py-3 text-left text-xs font-medium text-gray-500 uppercase">Customer</th><th class="px-4 py-3 text-left text-xs font-medium text-gray-500 uppercase">Stock After</th></tr></thead><tbody id="historyTableBody" class="bg-white divide-y divide-gray-200"></tbody></table></div><div class="flex items-center justify-end pt-6"><button id="closeHistoryModalBtn" type="button" class="bg-gray-200 text-gray-800 font-semibold py-2 px-6 rounded-lg hover:bg-gray-300">Close</button></div></div></div>
    <div id="messageBox" class="fixed bottom-5 right-5 bg-red-500 text-white py-3 px-5 rounded-lg shadow-xl transition-transform transform translate-x-full hidden"><p id="messageText"></p></div>

    <script type="module">
        // Firebase Imports
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getFirestore, collection, addDoc, getDocs, doc, updateDoc, deleteDoc, onSnapshot, writeBatch, serverTimestamp, query, orderBy } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";
        import { getAuth, onAuthStateChanged, createUserWithEmailAndPassword, signInWithEmailAndPassword, signOut, sendPasswordResetEmail } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";

        // --- YOUR CORRECT, VERIFIED CONFIG ---
        const firebaseConfig = {
          apiKey: "AIzaSyBqvd38A2xTbvmvuJWVBXhc83NcVuH4LaM",
          authDomain: "metal-stock-faf6c.firebaseapp.com",
          projectId: "metal-stock-faf6c",
          storageBucket: "metal-stock-faf6c.firebasestorage.app",
          messagingSenderId: "531401442922",
          appId: "1:531401442922:web:0d8159091329069ec2ac13",
          measurementId: "G-4E2Y6R3P8W"
        };

        const app = initializeApp(firebaseConfig);
        const db = getFirestore(app);
        const auth = getAuth(app);
        
        document.addEventListener('DOMContentLoaded', () => {
            // --- State Variables ---
            let userId = null;
            let inventoryCollectionRef = null;
            let fullInventory = [];
            const LOW_STOCK_THRESHOLD = 10;
            let isLoginMode = true;

            // --- DOM Elements ---
            const authContainer = document.getElementById('authContainer');
            const appContainer = document.getElementById('appContainer');
            const authForm = document.getElementById('authForm');
            const authTitle = document.getElementById('authTitle');
            const authSubmitBtn = document.getElementById('authSubmitBtn');
            const signOutBtn = document.getElementById('signOutBtn');
            const userEmail = document.getElementById('userEmail');
            const addItemForm = document.getElementById('addItemForm');
            const inventoryTableBody = document.getElementById('inventoryTableBody');
            const searchInput = document.getElementById('searchInput');
            const transactionModal = document.getElementById('transactionModal');
            const closeModalBtn = document.getElementById('closeModalBtn');
            const transactionForm = document.getElementById('transactionForm');
            const modalItemId = document.getElementById('modalItemId');
            const modalItemInfo = document.getElementById('modalItemInfo');
            const messageBox = document.getElementById('messageBox');
            const messageText = document.getElementById('messageText');
            const loadingOverlay = document.getElementById('loadingOverlay');
            const transactionType = document.getElementById('transactionType');
            const customerNameContainer = document.getElementById('customerNameContainer');
            const historyModal = document.getElementById('historyModal');
            const historyItemInfo = document.getElementById('historyItemInfo');
            const historyTableBody = document.getElementById('historyTableBody');
            const closeHistoryModalBtn = document.getElementById('closeHistoryModalBtn');
            const authToggleLink = document.getElementById('authToggleLink');
            const forgotPasswordLink = document.getElementById('forgotPasswordLink');

            // --- Authentication Logic ---
            onAuthStateChanged(auth, (user) => { 
                if (user) { 
                    userId = user.uid; 
                    userEmail.textContent = user.email; 
                    inventoryCollectionRef = collection(db, `users/${userId}/inventory`); 
                    authContainer.classList.add('hidden-by-auth'); 
                    appContainer.classList.remove('hidden-by-auth'); 
                    listenForInventoryUpdates(); 
                } else { 
                    userId = null; 
                    inventoryCollectionRef = null; 
                    fullInventory = []; 
                    renderInventory([]); 
                    authContainer.classList.remove('hidden-by-auth'); 
                    appContainer.classList.add('hidden-by-auth'); 
                } 
            });

            authForm.addEventListener('submit', async (e) => { 
                e.preventDefault(); 
                const email = authForm.email.value; 
                const password = authForm.password.value; 
                loadingOverlay.style.display = 'flex'; 
                try { 
                    if (isLoginMode) { 
                        await signInWithEmailAndPassword(auth, email, password); 
                    } else { 
                        await createUserWithEmailAndPassword(auth, email, password); 
                    } 
                } catch (error) { 
                    showMessage(error.message, true); 
                } finally { 
                    loadingOverlay.style.display = 'none'; 
                } 
            });

            signOutBtn.addEventListener('click', async () => { 
                await signOut(auth); 
            });

            authToggleLink.addEventListener('click', (e) => {
                e.preventDefault();
                isLoginMode = !isLoginMode;
                const forgotPasswordContainer = forgotPasswordLink.parentElement.parentElement;
                if (isLoginMode) {
                    authTitle.textContent = 'Sign in to your account';
                    authSubmitBtn.textContent = 'Sign In';
                    authToggleLink.innerHTML = 'Don\'t have an account? Sign Up';
                    forgotPasswordContainer.style.display = 'flex';
                } else {
                    authTitle.textContent = 'Create a new account';
                    authSubmitBtn.textContent = 'Sign Up';
                    authToggleLink.innerHTML = 'Already have an account? Sign In';
                    forgotPasswordContainer.style.display = 'none';
                }
            });

            forgotPasswordLink.addEventListener('click', async (e) => { 
                e.preventDefault(); 
                const email = authForm.email.value; 
                if (!email) { 
                    showMessage("Please enter your email address in the email field first.", true); 
                    return; 
                } 
                loadingOverlay.style.display = 'flex'; 
                try { 
                    await sendPasswordResetEmail(auth, email); 
                    showMessage(`Password reset link sent to ${email}. Please check your inbox.`); 
                } catch (error) { 
                    showMessage(error.message, true); 
                } finally { 
                    loadingOverlay.style.display = 'none'; 
                } 
            });

            // --- Main App Logic ---
            function showMessage(message, isError = false) { 
                messageText.textContent = message; 
                messageBox.className = `fixed bottom-5 right-5 text-white py-3 px-5 rounded-lg shadow-xl transition-transform transform ${isError ? 'bg-red-500' : 'bg-green-500'}`; 
                messageBox.classList.remove('hidden', 'translate-x-full'); 
                setTimeout(() => { 
                    messageBox.classList.add('translate-x-full'); 
                    setTimeout(() => messageBox.classList.add('hidden'), 300); 
                }, 4000); 
            }

            function listenForInventoryUpdates() { 
                if (!inventoryCollectionRef) return; 
                onSnapshot(inventoryCollectionRef, (snapshot) => { 
                    fullInventory = []; 
                    snapshot.forEach(doc => fullInventory.push({ id: doc.id, ...doc.data() })); 
                    filterAndRender(); 
                }, (error) => { 
                    console.error("Error listening to inventory:", error); 
                    showMessage("Could not fetch real-time inventory updates.", true); 
                }); 
            }

            function filterAndRender() { 
                const searchTerm = searchInput.value.toLowerCase(); 
                if (!fullInventory) return; 
                const filtered = fullInventory.filter(item => (item.material?.toLowerCase().includes(searchTerm) || item.shape?.toLowerCase().includes(searchTerm) || item.dimensions?.toLowerCase().includes(searchTerm))); 
                renderInventory(filtered); 
            }

            function renderInventory(items) {
                inventoryTableBody.innerHTML = '';
                if (!items || items.length === 0) { 
                    inventoryTableBody.innerHTML = '<tr><td colspan="5" class="text-center py-10 text-gray-500">No items in inventory.</td></tr>'; 
                    return; 
                }
                items.sort((a, b) => a.material.localeCompare(b.material));
                items.forEach(item => {
                    const row = document.createElement('tr');
                    row.className = item.currentStock < LOW_STOCK_THRESHOLD ? 'bg-red-100' : 'bg-white';
                    row.innerHTML = `
                        <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">${item.material}</td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-600">${item.shape}</td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-600">${item.dimensions}</td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm font-semibold ${item.currentStock < LOW_STOCK_THRESHOLD ? 'text-red-600' : 'text-gray-800'}">${item.currentStock}</td>
                        <td class="px-6 py-4 whitespace-nowrap text-center text-sm font-medium space-x-2">
                            <button class="text-green-600 hover:text-green-900 history-btn" data-id="${item.id}">History</button>
                            <button class="text-indigo-600 hover:text-indigo-900 stock-btn" data-id="${item.id}">Manage Stock</button>
                            <button class="text-red-600 hover:text-red-900 delete-btn" data-id="${item.id}">Delete</button>
                        </td>
                    `;
                    inventoryTableBody.appendChild(row);
                });
            }

            // --- Event Listeners for Main App ---
            addItemForm.addEventListener('submit', async (e) => {
                e.preventDefault();
                loadingOverlay.style.display = 'flex';
                const newItem = {
                    material: addItemForm.material.value,
                    shape: addItemForm.shape.value,
                    dimensions: addItemForm.dimensions.value,
                    initialStock: Number(addItemForm.initialStock.value),
                    currentStock: Number(addItemForm.initialStock.value),
                    timestamp: serverTimestamp()
                };

                try {
                    await addDoc(inventoryCollectionRef, newItem);
                    showMessage("Item added successfully!");
                    addItemForm.reset();
                } catch (e) {
                    console.error("Error adding document: ", e);
                    showMessage("Error adding item. Please try again.", true);
                } finally {
                    loadingOverlay.style.display = 'none';
                }
            });

            searchInput.addEventListener('input', filterAndRender);
            
            // Event Delegation for dynamically created buttons
            inventoryTableBody.addEventListener('click', async (e) => {
                if (e.target.classList.contains('stock-btn')) {
                    const itemId = e.target.dataset.id;
                    const item = fullInventory.find(i => i.id === itemId);
                    if (item) {
                        modalItemId.value = itemId;
                        modalItemInfo.textContent = `${item.material} ${item.shape} (${item.dimensions})`;
                        transactionModal.classList.add('active');
                        document.getElementById('transactionDate').valueAsDate = new Date(); // Set to current date
                    }
                } else if (e.target.classList.contains('delete-btn')) {
                    if (confirm('Are you sure you want to delete this item?')) {
                        const itemId = e.target.dataset.id;
                        loadingOverlay.style.display = 'flex';
                        try {
                            const docRef = doc(db, `users/${userId}/inventory`, itemId);
                            await deleteDoc(docRef);
                            showMessage("Item deleted successfully.");
                        } catch (e) {
                            console.error("Error deleting document: ", e);
                            showMessage("Error deleting item. Please try again.", true);
                        } finally {
                            loadingOverlay.style.display = 'none';
                        }
                    }
                } else if (e.target.classList.contains('history-btn')) {
                    const itemId = e.target.dataset.id;
                    const item = fullInventory.find(i => i.id === itemId);
                    if (item) {
                        historyItemInfo.textContent = `${item.material} ${item.shape} (${item.dimensions})`;
                        await fetchHistory(itemId);
                        historyModal.classList.add('active');
                    }
                }
            });

            transactionType.addEventListener('change', (e) => {
                if (e.target.value === 'outward') {
                    customerNameContainer.style.display = 'block';
                } else {
                    customerNameContainer.style.display = 'none';
                }
            });

            transactionForm.addEventListener('submit', async (e) => {
                e.preventDefault();
                loadingOverlay.style.display = 'flex';

                const itemId = modalItemId.value;
                const transactionTypeVal = transactionType.value;
                const transactionAmount = Number(document.getElementById('transactionAmount').value);
                const transactionDate = document.getElementById('transactionDate').value;
                const customerName = document.getElementById('customerName').value;

                const itemRef = doc(db, `users/${userId}/inventory`, itemId);
                const item = fullInventory.find(i => i.id === itemId);
                
                if (!item) {
                    showMessage("Item not found.", true);
                    loadingOverlay.style.display = 'none';
                    return;
                }

                const batch = writeBatch(db);
                const newStock = transactionTypeVal === 'inward' ? item.currentStock + transactionAmount : item.currentStock - transactionAmount;

                if (newStock < 0) {
                    showMessage("Outward quantity cannot exceed current stock.", true);
                    loadingOverlay.style.display = 'none';
                    return;
                }

                // Update inventory
                batch.update(itemRef, { currentStock: newStock });

                // Add transaction history
                const historyRef = collection(db, `users/${userId}/inventory/${itemId}/history`);
                batch.set(doc(historyRef), {
                    type: transactionTypeVal,
                    amount: transactionAmount,
                    stockAfter: newStock,
                    customer: transactionTypeVal === 'outward' ? customerName : 'N/A',
                    date: transactionDate,
                    timestamp: serverTimestamp()
                });

                try {
                    await batch.commit();
                    showMessage("Stock updated successfully!");
                    transactionModal.classList.remove('active');
                    transactionForm.reset();
                } catch (e) {
                    console.error("Error performing transaction: ", e);
                    showMessage("Error updating stock. Please try again.", true);
                } finally {
                    loadingOverlay.style.display = 'none';
                }
            });

            async function fetchHistory(itemId) {
                historyTableBody.innerHTML = '';
                const historyRef = collection(db, `users/${userId}/inventory/${itemId}/history`);
                const q = query(historyRef, orderBy("timestamp", "desc"));
                
                loadingOverlay.style.display = 'flex';
                try {
                    const querySnapshot = await getDocs(q);
                    if (querySnapshot.empty) {
                        historyTableBody.innerHTML = '<tr><td colspan="5" class="text-center py-10 text-gray-500">No transaction history.</td></tr>';
                        return;
                    }
                    querySnapshot.forEach(doc => {
                        const history = doc.data();
                        const row = document.createElement('tr');
                        row.innerHTML = `
                            <td class="px-4 py-3 whitespace-nowrap text-sm text-gray-800">${history.date}</td>
                            <td class="px-4 py-3 whitespace-nowrap text-sm text-gray-800">${history.type}</td>
                            <td class="px-4 py-3 whitespace-nowrap text-sm text-gray-800">${history.amount}</td>
                            <td class="px-4 py-3 whitespace-nowrap text-sm text-gray-800">${history.customer}</td>
                            <td class="px-4 py-3 whitespace-nowrap text-sm text-gray-800">${history.stockAfter}</td>
                        `;
                        historyTableBody.appendChild(row);
                    });
                } catch (e) {
                    console.error("Error fetching history: ", e);
                    showMessage("Could not fetch history.", true);
                } finally {
                    loadingOverlay.style.display = 'none';
                }
            }

            // --- Modal Controls ---
            closeModalBtn.addEventListener('click', () => transactionModal.classList.remove('active'));
            closeHistoryModalBtn.addEventListener('click', () => historyModal.classList.remove('active'));
        });
    </script>
</body>
</html>
