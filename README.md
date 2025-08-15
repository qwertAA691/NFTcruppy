<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Секретный Информатор</title>
<style>
    body {
        margin: 0;
        font-family: Arial, sans-serif;
        background: #0a0a0a;
        color: white;
    }
    .screen {
        display: none;
        min-height: 100vh;
        padding-top: 50px;
        text-align: center;
    }
    .active { display: block; }
    h2 { margin-bottom: 20px; }
    button {
        padding: 10px 25px;
        font-size: 16px;
        border: none;
        border-radius: 8px;
        cursor: pointer;
        margin: 8px;
        transition: 0.3s;
    }
    button:hover { transform: scale(1.05); }
    .menu-btn {
        background: #222;
        color: white;
        border: 1px solid #555;
    }
    .info-row {
        display: flex;
        justify-content: space-between;
        align-items: center;
        background: #1a1a1a;
        padding: 10px;
        margin: 8px auto;
        width: 350px;
        border-radius: 6px;
    }
    .tag {
        padding: 3px 8px;
        border-radius: 4px;
        font-size: 12px;
        white-space: nowrap;
    }
    .free { background: #4caf50; }
    .first { background: gold; color: black; }
    .second { background: #ff00ff; }
    .symbol { font-size: 18px; }
    #username-display {
        position: fixed;
        top: 10px;
        right: 15px;
        background: rgba(255,255,255,0.05);
        padding: 6px 10px;
        border-radius: 6px;
        font-size: 14px;
        color: gold;
    }
    #logout-btn {
        display: block;
        font-size: 12px;
        color: red;
        background: none;
        border: none;
        cursor: pointer;
        margin-top: 4px;
    }
    input {
        padding: 8px;
        font-size: 14px;
        border-radius: 6px;
        border: none;
        margin: 5px;
    }
    .price-container {
        display: flex;
        justify-content: center;
        gap: 15px;
        margin-top: 20px;
    }
    .price-option {
        background: #222;
        padding: 15px;
        border-radius: 8px;
        cursor: pointer;
        width: 200px;
        transition: 0.3s;
        border: 1px solid #555;
    }
    .price-option:hover { transform: scale(1.05); }
</style>
</head>
<body>

<div id="username-display" style="display:none;">
    <span id="username-text"></span>
    <button id="logout-btn" onclick="logout()">Выйти</button>
</div>

<!-- Экран старт -->
<div id="start-screen" class="screen active">
    <button style="background:linear-gradient(45deg,#ff5f5f,#ff8f8f);" onclick="goToAuth()">НАЧАТЬ</button>
</div>

<!-- Экран авторизация -->
<div id="auth-screen" class="screen">
    <h2>Вход или регистрация</h2>
    <input type="text" id="auth-username" placeholder="Username"><br>
    <input type="password" id="auth-password" placeholder="Пароль"><br>
    <button onclick="login()">Войти</button>
    <button onclick="register()">Регистрация</button>
</div>

<!-- Главное меню -->
<div id="main-menu" class="screen">
    <h2>Главное меню</h2>
    <button class="menu-btn" onclick="showScreen('functions-screen')">Функции</button>
    <button class="menu-btn" onclick="showScreen('buy-screen')">Купить подписку</button>
    <button class="menu-btn" onclick="showScreen('ref-screen')">Реферальная программа</button>
    <button class="menu-btn" onclick="showScreen('support-screen')">Поддержка</button>
    <button class="menu-btn" onclick="logout()">Выйти</button>
</div>

<!-- Экран функций -->
<div id="functions-screen" class="screen">
    <h2>Доступные функции</h2>
    <div class="info-row">
        <span>📅 Дата выхода подарков</span>
        <span class="symbol" id="check-0">❌</span>
    </div>
    <div class="info-row">
        <span>📰 NFT каналы + боты</span>
        <span class="symbol" id="check-1">❌</span>
    </div>
    <div class="info-row">
        <span>🤖 Автоскупка + скрипты</span>
        <span class="symbol" id="check-2">❌</span>
    </div>
    <div class="info-row">
        <span>💎 Скрипт от автора</span>
        <span class="symbol" id="check-3">❌</span>
    </div>
    <button onclick="showScreen('main-menu')">Назад</button>
</div>

<!-- Экран покупки -->
<div id="buy-screen" class="screen">
    <h2>Выберите подписку</h2>
    <div class="price-container">
        <div class="price-option" onclick="buySub('first')">
            <h3>Первый класс</h3>
            <p>Доступ к NFT каналам и ботам</p>
            <p>Цена: 2000 звёзд / ₽ / $</p>
        </div>
        <div class="price-option" onclick="buySub('second')">
            <h3>Второй класс</h3>
            <p>Всё + Премиум скрипт от автора</p>
            <p>Цена: 10000 звёзд / ₽ / $</p>
        </div>
    </div>
    <button onclick="showScreen('main-menu')">Назад</button>
</div>

<!-- Реферальная -->
<div id="ref-screen" class="screen">
    <h2>Реферальная программа</h2>
    <p>Обратись к менеджеру @Juilly для получения подробностей.</p>
    <button onclick="showScreen('main-menu')">Назад</button>
</div>

<!-- Поддержка -->
<div id="support-screen" class="screen">
    <h2>Поддержка</h2>
    <p>Напишите менеджеру @Juilly для помощи.</p>
    <button onclick="showScreen('main-menu')">Назад</button>
</div>

<script>
let users = JSON.parse(localStorage.getItem('users') || '{}');
let currentUser = null;

function goToAuth() {
    showScreen('auth-screen');
}

function register() {
    let u = document.getElementById('auth-username').value;
    let p = document.getElementById('auth-password').value;
    if (!u || !p) { alert('Введите все данные'); return; }
    if (users[u]) { alert('Пользователь уже существует'); return; }
    users[u] = { password: p, sub: null };
    localStorage.setItem('users', JSON.stringify(users));
    alert('Регистрация успешна');
}

function login() {
    let u = document.getElementById('auth-username').value;
    let p = document.getElementById('auth-password').value;
    if (users[u] && users[u].password === p) {
        currentUser = u;
        localStorage.setItem('currentUser', u);
        showUsername();
        showScreen('main-menu');
        updateChecks();
    } else {
        alert('Неверные данные');
    }
}

function logout() {
    currentUser = null;
    localStorage.removeItem('currentUser');
    document.getElementById('username-display').style.display = 'none';
    showScreen('start-screen');
}

function showScreen(id) {
    document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    if (id === 'functions-screen') updateChecks();
}

function showUsername() {
    document.getElementById('username-display').style.display = 'block';
    document.getElementById('username-text').innerText = currentUser;
}

function buySub(type) {
    if (!currentUser) return;
    users[currentUser].sub = type;
    localStorage.setItem('users', JSON.stringify(users));
    alert('Подписка оформлена: ' + type);
    updateChecks();
}

function updateChecks() {
    let sub = users[currentUser]?.sub;
    document.getElementById('check-0').innerText = '✅'; // бесплатная
    document.getElementById('check-1').innerText = (sub === 'first' || sub === 'second') ? '✅' : '❌';
    document.getElementById('check-2').innerText = (sub === 'second') ? '✅' : '❌';
    document.getElementById('check-3').innerText = (sub === 'second') ? '✅' : '❌';
}

// авто вход
window.onload = () => {
    let saved = localStorage.getItem('currentUser');
    if (saved && users[saved]) {
        currentUser = saved;
        showUsername();
        showScreen('main-menu');
    }
};
</script>

</body>
</html>
