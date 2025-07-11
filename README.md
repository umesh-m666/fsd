<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Login & Dashboard</title>
  <style>
    body {
      margin: 0;
      height: 100vh;
      background-image: url('https://wallpapers.com/images/hd/anime-landscape-background-12xkkc2cnstc3gp2.jpg');
      background-size: cover;
      background-repeat: no-repeat;
      background-position: center;
      font-family: Arial, sans-serif;
      color: white;
      display: flex;
      justify-content: center;
      align-items: center;
      text-align: center;
    }

    #container {
      background-color: rgba(0, 0, 0, 0.6);
      padding: 30px;
      border-radius: 15px;
      width: 90%;
      max-width: 500px;
      box-sizing: border-box;
      position: relative;
    }

    form {
      display: flex;
      flex-direction: column;
      margin-bottom: 20px;
      text-align: left;
    }

    label {
      margin-bottom: 5px;
      color: white;
    }

    .input-wrapper {
      position: relative;
      margin-bottom: 15px;
    }

    .input-wrapper input {
      width: 100%;
      padding: 10px;
      padding-right: 40px;
      font-size: 1em;
      border: none;
      border-radius: 5px;
      box-sizing: border-box;
    }

    .input-wrapper span {
      position: absolute;
      right: 10px;
      top: 50%;
      transform: translateY(-50%);
      cursor: pointer;
      user-select: none;
      color: white;
    }

    input:focus {
      outline: 2px solid #80bdff;
      background-color: #222;
      color: rgb(243, 237, 237);
    }

    button {
      padding: 10px;
      font-size: 1em;
      margin: 10px 0;
      cursor: pointer;
      background-color: #007bff;
      color: rgb(252, 248, 248);
      border: none;
      border-radius: 5px;
      transition: background-color 0.3s ease;
    }

    button:hover {
      background-color: #0056b3;
    }

    .error {
      color: #ff6b6b;
      margin-bottom: 10px;
      font-weight: bold;
    }

    .link {
      color: #80bdff;
      cursor: pointer;
      text-decoration: underline;
      margin-top: 10px;
      display: inline-block;
    }

    .btn-container {
      display: flex;
      flex-direction: column;
      align-items: center;
    }

    h2 {
      margin-bottom: 20px;
    }

    #logoutBtn {
      position: fixed;
      top: 20px;
      right: 20px;
      background-color: #dc3545;
      padding: 8px 12px;
      font-size: 1em;
      border-radius: 5px;
      border: none;
      cursor: pointer;
      color: white;
      z-index: 1000;
      transition: background-color 0.3s ease;
    }

    #logoutBtn:hover {
      background-color: #a71d2a;
    }
  </style>
</head>
<body>
  <div id="container">

    <!-- Login View -->
    <div id="loginView">
      <h2>Login</h2>
      <form id="loginForm">
        <p class="error" id="loginError"></p>
        <label for="loginUsername">Username:</label>
        <div class="input-wrapper">
          <input type="text" id="loginUsername" required />
        </div>

        <label for="loginPassword">Password:</label>
        <div class="input-wrapper">
          <input type="password" id="loginPassword" required />
          <span onclick="togglePassword('loginPassword', this)">👁️</span>
        </div>

        <button type="submit">Login</button>
      </form>
      <span class="link" id="goToRegister">Don't have an account? Register</span>
    </div>

    <!-- Register View -->
    <div id="registerView" style="display:none;">
      <h2>Register</h2>
      <form id="registerForm">
        <p class="error" id="registerError"></p>
        <label for="regUsername">Username:</label>
        <div class="input-wrapper">
          <input type="text" id="regUsername" required />
        </div>

        <label for="regPassword">Password:</label>
        <div class="input-wrapper">
          <input type="password" id="regPassword" required />
          <span onclick="togglePassword('regPassword', this)">👁️</span>
        </div>

        <button type="submit">Register</button>
      </form>
      <span class="link" id="goToLogin">Already have an account? Login</span>
    </div>

    <!-- Dashboard View -->
    <div id="dashboardView" style="display:none;">
      <h2>Dashboard</h2>
      <p>Welcome, <strong><span id="userDisplay"></span></strong>! 🎉</p>
      <div class="btn-container">
        <button onclick="goToHome()">Go to Home Page</button>
      </div>
      <button id="logoutBtn" onclick="logout()">Logout</button>
    </div>

  </div>

  <script>
    const loginView = document.getElementById('loginView');
    const registerView = document.getElementById('registerView');
    const dashboardView = document.getElementById('dashboardView');

    const loginForm = document.getElementById('loginForm');
    const registerForm = document.getElementById('registerForm');
    const loginError = document.getElementById('loginError');
    const registerError = document.getElementById('registerError');
    const goToRegister = document.getElementById('goToRegister');
    const goToLogin = document.getElementById('goToLogin');
    const userDisplay = document.getElementById('userDisplay');

    function show(view) {
      loginView.style.display = 'none';
      registerView.style.display = 'none';
      dashboardView.style.display = 'none';
      view.style.display = 'block';
    }

    goToRegister.onclick = () => {
      registerForm.reset();
      loginError.textContent = '';
      show(registerView);
    };

    goToLogin.onclick = () => {
      loginForm.reset();
      registerError.textContent = '';
      show(loginView);
    };

    registerForm.onsubmit = (e) => {
      e.preventDefault();
      const username = document.getElementById('regUsername').value.trim();
      const password = document.getElementById('regPassword').value;

      if (!username || !password) {
        registerError.textContent = 'Please fill in both fields.';
        return;
      }

      const users = JSON.parse(localStorage.getItem('users') || '{}');
      if (users[username]) {
        registerError.textContent = 'Username already exists.';
        return;
      }

      users[username] = password;
      localStorage.setItem('users', JSON.stringify(users));
      alert('Registration successful! Please log in.');
      show(loginView);
    };

    loginForm.onsubmit = (e) => {
      e.preventDefault();
      const username = document.getElementById('loginUsername').value.trim();
      const password = document.getElementById('loginPassword').value;

      const users = JSON.parse(localStorage.getItem('users') || '{}');
      if (users[username] === password) {
        localStorage.setItem('loggedInUser', username);
        userDisplay.textContent = username;
        show(dashboardView);
      } else {
        loginError.textContent = 'Invalid username or password.';
      }
    };

    function goToHome() {
      window.location.href = 'home.html';
    }

    function logout() {
      localStorage.removeItem('loggedInUser');
      show(loginView);
    }

    function togglePassword(inputId, iconElement) {
      const input = document.getElementById(inputId);
      if (input.type === 'password') {
        input.type = 'text';
        iconElement.textContent = '🙈';
      } else {
        input.type = 'password';
        iconElement.textContent = '👁️';
      }
    }

    window.onload = () => {
      const user = localStorage.getItem('loggedInUser');
      if (user) {
        userDisplay.textContent = user;
        show(dashboardView);
      } else {
        show(loginView);
      }
    };
  </script>
</body>
</html>

