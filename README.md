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
        text-align: center;
    }
    .screen {
        display: none;
        min-height: 100vh;
        padding-top: 50px;
    }
    .active {
        display: flex;
        flex-direction: column;
        align-items: center;
    }
    button {
        padding: 12px 30px;
        margin: 8px;
        font-size: 18px;
        border: none;
        border-radius: 8px;
        cursor: pointer;
        transition: 0.3s;
    }
    button:hover { transform: scale(1.05); }
    .info-row {
        display: flex;
        justify-content: space-between;
        align-items: center;
        width: 320px;
        background: #1a1a1a;
        padding: 10px;
        margin: 8px;
        border-radius: 8px;
    }
    .info-btn { flex: 1; margin-right: 8px; }
    .tag {
        font-size: 14px;
        padding: 4px 6px;
        border-radius: 4px;
        white-space: nowrap;
    }
    .free { background: #4caf50; }
    .first { background: gold; color: black; }
    .second { background: #ff00ff; }
    #username-display {
        position: fixed;
        top: 10px;
        right: 15px;
        background: rgba(255,255,255,0.1);
        padding: 5px 10px;
        border-radius: 6px;
        font-size: 14px;
        color: gold;
        display: none;
    }
    input {
        padding: 10px;
        font-size: 16px;
        border-radius: 6px;
        border: none;
        margin: 5px;
    }
    .price-option {
        background: linear-gradient(45deg, #333, #555);
        color: white;
        padding: 15px;
        border-radius: 8px;
        cursor: pointer;
        width: 260px;
        margin: 8px 0;
        transition: 0.3s;
    }
    .price-option:hover { transform: scale(1.05); }
</style>
</head>
<body>

<div id="username-display"></div>

<div id="start-screen" class="screen active">
    <button style="background:linear-gradient(45deg,#ff5f5f,#ff8f8f);color:white;box-shadow:0 0 20px rgba(255,80,80,0.8);" onclick="goToAuth()">НАЧАТЬ</button>
</div>

<div id="auth-screen" class="screen">
    <h2>Вход или регистрация</h2>
    <input type="text" id="auth-username" placeholder="Username"><br>
    <input type="password" id="auth-password" placeholder="Пароль"><br>
    <button onclick="login()">Войти</button>
    <button onclick="register()">Регистрация</button>
</div>

<div id="info-screen" class="screen">
    <h2>Выберите информацию</h2>
    <div class="info-row">
        <button class="info-btn" onclick="getInfo(0)">📅 Дата выхода подарков</button>
        <div class="tag free">Бесплатно</div>
    </div>
    <div class="info-row">
        <button class="info-btn" onclick="getInfo(1)">📰 NFT каналы + боты</button>
        <div class="tag first">Первый класс</div>
    </div>
    <div class="info-row">
        <button class="info-btn" onclick="getInfo(2)">👑 Автоскупка и скрипты</button>
        <div class="tag second">Второй класс</div>
    </div>
    <div class="info-row">
        <button class="info-btn" onclick="getInfo(3)">💎 Премиум функции</button>
        <div class="tag second">Второй класс</div>
    </div>
</div>

<div id="buy-screen" class="screen">
    <h2>Купить подписку</h2>
    <div class="price-option" onclick="buySub('first')">Первый класс — 2000 ⭐ / ₽ / $</div>
    <div class="price-option" onclick="buySub('second')">Второй класс — 10000 ⭐ / ₽ / $</div>
    <button onclick="goToInfo()">Назад</button>
</div>

<script>
let users = JSON.parse(localStorage.getItem('users')) || {};
let currentUser = null;

function showScreen(id) {
    document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
    document.getElementById(id).classList.add('active');
}

function goToAuth() {
    showScreen('auth-screen');
}

function register() {
    let u = document.getElementById('auth-username').value;
    let p = document.getElementById('auth-password').value;
    if (!u || !p) return alert("Введите логин и пароль");
    if (users[u]) return alert("Пользователь уже есть");
    users[u] = { password: p, sub: 'none' };
    localStorage.setItem('users', JSON.stringify(users));
    alert("Регистрация успешна");
}

function login() {
    let u = document.getElementById('auth-username').value;
    let p = document.getElementById('auth-password').value;
    if (!users[u] || users[u].password !== p) return alert("Неверные данные");
    currentUser = u;
    document.getElementById('username-display').textContent = u;
    document.getElementById('username-display').style.display = 'block';
    showScreen('info-screen');
}

function getInfo(id) {
    let sub = users[currentUser].sub;
    let allowed = {
        none: [0],
        first: [0,1],
        second: [0,1,2,3]
    };
    if (allowed[sub].includes(id)) {
        let infoTexts = [
            "Дата выхода новых Telegram подарков: ожидается в конце месяца.",
            "Список топ NFT каналов и ботов: ...",
            "Боты для автоскупки и скрипты: ...",
            "Премиум функции: аналитика подарков, авто-уведомления и многое другое."
        ];
        alert(infoTexts[id]);
    } else {
        showScreen('buy-screen');
    }
}

function buySub(type) {
    users[currentUser].sub = type;
    localStorage.setItem('users', JSON.stringify(users));
    alert("Подписка активирована!");
    showScreen('info-screen');
}

function goToInfo() {
    showScreen('info-screen');
}
</script>
</body>
</html>
