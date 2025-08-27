<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Inventory Login</title>
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-100">

    <!-- Auth Screen -->
    <div id="authContainer" class="min-h-screen flex items-center justify-center">
        <div class="max-w-md w-full space-y-8 bg-white p-10 rounded-xl shadow-lg">
            <div>
                <h2 id="authTitle" class="mt-6 text-center text-3xl font-bold text-gray-900">Sign In</h2>
            </div>
            <form id="authForm" class="mt-8 space-y-6">
                <div class="rounded-md shadow-sm -space-y-px">
                    <div>
                        <label for="email-address" class="sr-only">Email address</label>
                        <input id="email-address" name="email" type="email" required class="appearance-none rounded-none relative block w-full px-3 py-3 border border-gray-300 placeholder-gray-500 text-gray-900 rounded-t-md focus:outline-none focus:ring-indigo-500 focus:border-indigo-500" placeholder="Email address">
                    </div>
                    <div>
                        <label for="password" class="sr-only">Password</label>
                        <input id="password" name="password" type="password" required class="appearance-none rounded-none relative block w-full px-3 py-3 border border-gray-300 placeholder-gray-500 text-gray-900 rounded-b-md focus:outline-none focus:ring-indigo-500 focus:border-indigo-500" placeholder="Password">
                    </div>
                </div>
                <div>
                    <button id="authSubmitBtn" type="submit" class="group relative w-full flex justify-center py-3 px-4 border border-transparent text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700">
                        Sign In
                    </button>
                </div>
            </form>
            <p class="mt-2 text-center text-sm text-gray-600">
                <a href="#" id="authToggleLink" class="font-medium text-indigo-600 hover:text-indigo-500">
                    Don't have an account? Sign Up
                </a>
            </p>
        </div>
    </div>

    <!-- Logged In Screen -->
    <div id="appContainer" style="display: none;" class="text-center p-10">
        <h1 class="text-2xl font-bold">Welcome!</h1>
        <p id="userEmail" class="mt-2 text-lg"></p>
        <button id="signOutBtn" class="mt-4 bg-red-500 text-white font-semibold py-2 px-4 rounded-lg">Sign Out</button>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, onAuthStateChanged, createUserWithEmailAndPassword, signInWithEmailAndPassword, signOut } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";

        const firebaseConfig = {
          apiKey: "AIzaSyBqvd38A2xTbvmvuJWVBXhc83NcVuH4LaM",
          authDomain: "metal-stock-faf6c.firebaseapp.com",
          projectId: "metal-stock-faf6c",
          storageBucket: "metal-stock-faf6c.appspot.com",
          messagingSenderId: "531401442922",
          appId: "1:531401442922:web:0d8159091329069ec2ac13"
        };

        const app = initializeApp(firebaseConfig);
        const auth = getAuth(app);

        document.addEventListener('DOMContentLoaded', () => {
            const authContainer = document.getElementById('authContainer');
            const appContainer = document.getElementById('appContainer');
            const authForm = document.getElementById('authForm');
            const authToggleLink = document.getElementById('authToggleLink');
            const authTitle = document.getElementById('authTitle');
            const authSubmitBtn = document.getElementById('authSubmitBtn');
            const signOutBtn = document.getElementById('signOutBtn');
            const userEmail = document.getElementById('userEmail');

            let isLoginMode = true;

            onAuthStateChanged(auth, (user) => {
                if (user) {
                    authContainer.style.display = 'none';
                    appContainer.style.display = 'block';
                    userEmail.textContent = `You are logged in as: ${user.email}`;
                } else {
                    authContainer.style.display = 'flex';
                    appContainer.style.display = 'none';
                }
            });

            authForm.addEventListener('submit', async (e) => {
                e.preventDefault();
                const email = authForm.email.value;
                const password = authForm.password.value;
                try {
                    if (isLoginMode) {
                        await signInWithEmailAndPassword(auth, email, password);
                    } else {
                        await createUserWithEmailAndPassword(auth, email, password);
                    }
                } catch (error) {
                    alert(error.message);
                }
            });

            signOutBtn.addEventListener('click', async () => {
                await signOut(auth);
            });

            authToggleLink.addEventListener('click', (e) => {
                e.preventDefault();
                isLoginMode = !isLoginMode;
                if (isLoginMode) {
                    authTitle.textContent = 'Sign In';
                    authSubmitBtn.textContent = 'Sign In';
                    authToggleLink.textContent = 'Don\'t have an account? Sign Up';
                } else {
                    authTitle.textContent = 'Sign Up';
                    authSubmitBtn.textContent = 'Sign Up';
                    authToggleLink.textContent = 'Already have an account? Sign In';
                }
            });
        });
    </script>

</body>
</html>
