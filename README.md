<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Секретный информатор</title>
<style>
  :root{
    --bg:#070707; --panel:#0f0f12; --muted:#8a8a8a; --txt:#e8e8ea;
    --accent:#ff6b6b; --gold:#ffd54a; --violet:#c86bff; --ok:#21d07a; --warn:#ffb74d;
    --card:#13131a; --br:14px; --shadow:0 12px 40px rgba(0,0,0,.55);
  }
  *{box-sizing:border-box}
  html,body{height:100%}
  body{
    margin:0; font-family:Inter,system-ui,Arial,sans-serif; color:var(--txt);
    background:
      radial-gradient(1200px 600px at 120% -10%, rgba(100,0,255,.08), transparent 60%),
      radial-gradient(800px 400px at -10% 110%, rgba(255,200,0,.06), transparent 60%),
      linear-gradient(#060608,#09090c 30%, #0a0a0d);
  }
  .center-screen{
    min-height:100dvh; display:none; align-items:center; justify-content:center; flex-direction:column; gap:22px; padding:28px;
  }
  .active{display:flex}
  /* header badge */
  .user-badge{
    position:fixed; top:10px; right:12px; display:none; align-items:center; gap:8px;
    padding:6px 10px; border-radius:999px; background:rgba(255,255,255,.06);
    border:1px solid rgba(255,255,255,.08); color:var(--gold); font-size:13px; letter-spacing:.2px;
    backdrop-filter: blur(6px); cursor:pointer;
  }
  .user-menu{
    position:fixed; top:46px; right:12px; display:none; flex-direction:column; gap:6px;
    background:var(--panel); border:1px solid rgba(255,255,255,.08); border-radius:12px; padding:10px; width:164px;
    box-shadow:var(--shadow);
  }
  .user-menu button{width:100%}
  /* common UI */
  .card{
    width:min(860px,94vw); background:var(--card); border:1px solid rgba(255,255,255,.06);
    border-radius:var(--br); box-shadow:var(--shadow); padding:22px;
  }
  h1,h2{margin:.2em 0 .4em 0}
  p.muted{color:var(--muted); margin:.2em 0 1em}
  .btn{
    border:none; cursor:pointer; padding:14px 22px; border-radius:12px; font-weight:600; letter-spacing:.2px;
    background:#1b1b22; color:var(--txt); transition:transform .12s ease, box-shadow .2s ease, background .2s;
    box-shadow:0 10px 24px rgba(0,0,0,.4), inset 0 1px 0 rgba(255,255,255,.05);
  }
  .btn:hover{transform:translateY(-1px)}
  .btn:active{transform:translateY(0)}
  .btn-accent{background:linear-gradient(135deg, #ff4d6d, #d43bff); box-shadow:0 15px 40px rgba(212,59,255,.35)}
  .btn-gold{background:linear-gradient(135deg, #ffe082, #ffc107); color:#2b2100; box-shadow:0 15px 40px rgba(255,208,0,.35)}
  .btn-ghost{background:#14141a}
  .btn-danger{background:linear-gradient(135deg,#ff6b6b,#b71c1c)}
  .stack{display:flex; gap:12px; flex-wrap:wrap}
  .grid2{display:grid; grid-template-columns:1fr 1fr; gap:12px}
  @media (max-width:720px){ .grid2{grid-template-columns:1fr} }
  .input{
    width:100%; padding:14px 14px; border-radius:10px; background:#121219; border:1px solid rgba(255,255,255,.08); color:var(--txt);
  }
  .row{display:flex; align-items:center; justify-content:space-between; gap:12px}
  .row-start{display:flex; align-items:center; gap:12px}
  .pill{
    font-size:12px; padding:6px 10px; border-radius:999px; border:1px solid rgba(255,255,255,.08); background:#141421; color:#c8c8d0;
  }
  .pill.req-none{color:#a8ffac; border-color:rgba(33,208,122,.4)}
  .pill.req-1{color:#ffd54a; border-color:rgba(255,213,74,.45)}
  .pill.req-2{color:#d7a4ff; border-color:rgba(200,107,255,.45)}
  .list{display:grid; gap:14px; margin-top:10px}
  .tile{
    display:grid; grid-template-columns:1fr auto auto; align-items:center; gap:12px;
    padding:16px; border-radius:12px; border:1px solid rgba(255,255,255,.06); background:#111119;
    transition:transform .12s ease, box-shadow .2s ease; cursor:pointer;
  }
  .tile:hover{transform:translateY(-1px); box-shadow:0 10px 28px rgba(0,0,0,.35)}
  .tile .title{font-weight:700}
  .tile .preview{font-size:13px; color:var(--muted)}
  .divider{height:1px; background:rgba(255,255,255,.06); margin:14px 0}
  .notice{background:#12121b; border:1px dashed rgba(255,255,255,.18); padding:12px; border-radius:12px; color:#cbd5ff}
  .ok{color:var(--ok); font-weight:700}
  .no{color:#ff6b6b; font-weight:700}
  .tier-free{box-shadow: 0 0 0 1px rgba(150,150,150,.25) inset}
  .tier-1{background:linear-gradient(145deg, rgba(255,224,130,.12), rgba(255,193,7,.06))}
  .tier-2{background:linear-gradient(145deg, rgba(200,107,255,.16), rgba(100,50,180,.08))}
  .sheet{width:min(860px,94vw); background:var(--panel); border:1px solid rgba(255,255,255,.08); border-radius:16px; box-shadow:var(--shadow); padding:18px}
  .menu-grid{display:grid; grid-template-columns:repeat(2,1fr); gap:12px}
  @media (max-width:560px){ .menu-grid{grid-template-columns:1fr} }
  .pay-row{display:flex; gap:10px; flex-wrap:nowrap; overflow:auto; padding-bottom:6px}
  .pay-row .btn{white-space:nowrap}
  .upsell{display:none; margin-top:10px; padding:10px; border-radius:10px; background:#1a1a22; border:1px solid rgba(255,255,255,.08)}
</style>
</head>
<body>

<!-- floating username -->
<div id="userBadge" class="user-badge" onclick="toggleUserMenu()">
  <span id="badgeName">@user</span>
</div>
<div id="userMenu" class="user-menu">
  <button class="btn" onclick="go('profile')">Профиль</button>
  <button class="btn btn-danger" onclick="logout()">Выйти</button>
</div>

<!-- START -->
<section id="start" class="center-screen active">
  <div class="card" style="text-align:center; padding:36px 26px;">
    <h1 style="letter-spacing:.6px;">🔒 Секретный информатор</h1>
    <p class="muted">Тёмный доступ к инсайдам: релизы подарков, NFT-каналы, боты, автоскупка и премиум-скрипты.</p>
    <button class="btn btn-accent" onclick="go('lang')">НАЧАТЬ</button>
  </div>
</section>

<!-- LANGUAGE -->
<section id="lang" class="center-screen">
  <div class="card">
    <h2>Выберите язык</h2>
    <div class="stack">
      <button class="btn btn-ghost" onclick="setLang('ru')">Русский</button>
      <button class="btn btn-ghost" onclick="setLang('en')">English</button>
    </div>
  </div>
</section>

<!-- AUTH -->
<section id="auth" class="center-screen">
  <div class="card">
    <h2>Вход или регистрация</h2>
    <div class="stack" style="margin-bottom:8px">
      <button class="btn btn-ghost" onclick="switchAuth('login')">Войти</button>
      <button class="btn btn-ghost" onclick="switchAuth('register')">Зарегистрироваться</button>
      <button class="btn btn-ghost" onclick="switchAuth('key')">Войти по ключу</button>
    </div>
    <div id="authLogin" style="display:none">
      <input id="loginUser" class="input" placeholder="Username"/>
      <input id="loginPass" class="input" type="password" placeholder="Пароль"/>
      <div class="stack">
        <button class="btn btn-accent" onclick="login()">Войти</button>
      </div>
    </div>
    <div id="authRegister" style="display:none">
      <input id="regUser" class="input" placeholder="Username"/>
      <input id="regPass" class="input" type="password" placeholder="Пароль"/>
      <div class="stack">
        <button class="btn btn-accent" onclick="register()">Зарегистрироваться</button>
      </div>
    </div>
    <div id="authKey" style="display:none">
      <input id="keyInput" class="input" placeholder="Вставьте секретный ключ"/>
      <div class="stack">
        <button class="btn btn-accent" onclick="loginByKey()">Войти по ключу</button>
      </div>
      <p class="muted">Ключ можно скопировать из «Профиля» на другом устройстве.</p>
    </div>
  </div>
</section>

<!-- HOME MENU -->
<section id="home" class="center-screen">
  <div class="card">
    <h2>Меню</h2>
    <div class="menu-grid">
      <button class="btn" onclick="go('info')">⚙️ Функции</button>
      <button class="btn" onclick="go('pay')">💳 Купить подписку</button>
      <button class="btn" onclick="go('ref')">👥 Реферальная программа</button>
      <button class="btn" onclick="go('support')">🛠 Поддержка</button>
    </div>
    <div class="divider"></div>
    <div class="row">
      <div class="muted">Нажмите на @юзернейм в правом верхнем углу, чтобы открыть профиль или выйти.</div>
      <button class="btn btn-danger" onclick="logout()">Выйти</button>
    </div>
  </div>
</section>

<!-- PROFILE -->
<section id="profile" class="center-screen">
  <div class="card">
    <h2>Профиль</h2>
    <div class="row">
      <div>
        <div class="title" id="profileName">@user</div>
        <div class="muted">Подписка: <span id="profileSub">Нет</span></div>
      </div>
      <button class="btn" onclick="go('home')">В меню →</button>
    </div>
    <div class="divider"></div>
    <div class="notice">
      <b>Секретный ключ (для входа с другого устройства):</b>
      <div class="row" style="gap:8px; margin-top:8px">
        <input id="profileKey" class="input" readonly/>
        <button class="btn" onclick="copyKey()">Скопировать</button>
      </div>
      <div class="muted">Храните ключ в надёжном месте. Кто владеет ключом — владеет доступом.</div>
    </div>
    <div class="divider"></div>
    <div class="stack">
      <button class="btn" onclick="logout()">Выйти</button>
      <button class="btn" onclick="clearDevice()">Очистить устройство</button>
    </div>
  </div>
</section>

<!-- INFO SELECTOR -->
<section id="info" class="center-screen">
  <div class="card">
    <h2>Доступные функции</h2>
    <p class="muted">Короткое описание видно сразу, но ответы — только при достаточной подписке. Нужная подписка и доступность — справа.</p>

    <div class="list">
      <div class="tile tier-free" onclick="openInfo(1)" title="Сводка сигналов, ориентировочные окна запуска и чек-лист подготовки.">
        <div>
          <div class="title">📅 Дата выхода Telegram-подарков</div>
          <div class="preview">Окна запуска, поведенческие маркеры API и триггеры мониторинга.</div>
        </div>
        <div class="pill req-none">Без подписки</div>
        <div id="ent-1" class="ok">✓</div>
      </div>

      <div class="tile tier-1" onclick="openInfo(2)" title="Отобранные NFT-каналы, приватные фиды и звонящие боты при релизе.">
        <div>
          <div class="title">📰 NFT-каналы + звонящие боты</div>
          <div class="preview">Списки каналов с низким шумом, настройка звонков, тайм-зоны и фильтры.</div>
        </div>
        <div class="pill req-1">Нужна: Первый класс</div>
        <div id="ent-2" class="no">✗</div>
      </div>

      <div class="tile tier-2" onclick="openInfo(3)" title="Боты автоскупки, связки, анти-лаг скрипты, стратегии лимитов.">
        <div>
          <div class="title">🤖 Автоскупка + скрипты</div>
          <div class="preview">Сниппеты и практики: пред-подписи, ретраи, разделение платёжных источников.</div>
        </div>
        <div class="pill req-2">Нужна: Второй класс</div>
        <div id="ent-3" class="no">✗</div>
      </div>

      <div class="tile tier-2" onclick="openInfo(4)" title="Приоритетные каналы, мгновенные алерты, закрытые беты.">
        <div>
          <div class="title">👑 Премиум-функции</div>
          <div class="preview">Push+звонок+email, приватные каналы, ранний доступ к новому инструментарию.</div>
        </div>
        <div class="pill req-2">Нужна: Второй класс</div>
        <div id="ent-4" class="no">✗</div>
      </div>

      <div class="tile tier-2" onclick="openInfo(5)" title="Личный скрипт от автора, оптимизированный под свежие изменения.">
        <div>
          <div class="title">🧩 Скрипт от автора</div>
          <div class="preview">Быстрый шаблон + консультация по адаптации под вашу инфраструктуру.</div>
        </div>
        <div class="pill req-2">Нужна: Второй класс</div>
        <div id="ent-5" class="no">✗</div>
      </div>
    </div>

    <div id="upsell" class="upsell">
      Недостаточный уровень подписки.
      <div class="stack" style="margin-top:8px">
        <button class="btn btn-gold" onclick="go('pay')">Купить подписку</button>
        <button class="btn btn-ghost" onclick="hideUpsell()">Закрыть</button>
      </div>
    </div>

    <div class="divider"></div>
    <div class="row">
      <button class="btn" onclick="go('home')">← В меню</button>
      <div class="muted">Нет доступа? Оформите вечную подписку.</div>
      <button class="btn btn-gold" onclick="go('pay')">Купить подписку</button>
    </div>
  </div>
</section>

<!-- PURCHASE -->
<section id="pay" class="center-screen">
  <div class="sheet">
    <h2>Вечные подписки</h2>
    <div class="grid2">
      <div class="card tier-1">
        <h3>Первый класс</h3>
        <p class="muted">Открывает «NFT-каналы + звонящие боты». Идеально, чтобы ничего не пропустить в момент релиза.</p>
        <ul style="text-align:left; line-height:1.6">
          <li>Доступ к списку отобранных NFT-каналов</li>
          <li>Звонящие боты при ключевых событиях</li>
          <li>Шаблоны фильтров/уведомлений</li>
        </ul>
        <div class="divider"></div>
        <div class="row">
          <div class="title">Цена</div>
          <div class="pill">2000 ⭐ / 2000 ₽ / $2000</div>
        </div>
        <div class="divider"></div>
        <div class="muted">Способ оплаты</div>
        <div class="pay-row">
          <button class="btn" onclick="choosePlan(1,'stars')">⭐ Звёзды</button>
          <button class="btn" onclick="choosePlan(1,'rub')">₽ Рубли</button>
          <button class="btn" onclick="choosePlan(1,'usd')">$ Доллары</button>
        </div>
      </div>

      <div class="card tier-2">
        <h3>Второй класс</h3>
        <p class="muted">Полный набор: автоскупка, премиум-функции, личный скрипт от автора и консультации.</p>
        <ul style="text-align:left; line-height:1.6">
          <li>Боты автоскупки и анти-лаг практики</li>
          <li>Премиум-алерты и закрытые каналы</li>
          <li>🧩 Личный скрипт от автора + подсказки по внедрению</li>
        </ul>
        <div class="divider"></div>
        <div class="row">
          <div class="title">Цена</div>
          <div class="pill">10000 ⭐ / 10000 ₽ / $10000</div>
        </div>
        <div class="divider"></div>
        <div class="muted">Способ оплаты</div>
        <div class="pay-row">
          <button class="btn" onclick="choosePlan(2,'stars')">⭐ Звёзды</button>
          <button class="btn" onclick="choosePlan(2,'rub')">₽ Рубли</button>
          <button class="btn" onclick="choosePlan(2,'usd')">$ Доллары</button>
        </div>
      </div>
    </div>

    <div class="divider"></div>
    <div id="payConfirm" style="display:none">
      <div class="row">
        <div class="muted">Вы выбрали:</div>
        <div class="pill" id="paySummary"></div>
      </div>
      <div class="stack" style="margin-top:8px">
        <button class="btn btn-accent" onclick="doPay()">Оплатить</button>
        <button class="btn" onclick="cancelPay()">Отмена</button>
      </div>
    </div>

    <div class="row" style="margin-top:10px">
      <button class="btn" onclick="go('home')">← В меню</button>
      <button class="btn" onclick="go('info')">К функциям →</button>
    </div>
  </div>
</section>

<!-- REFERRAL -->
<section id="ref" class="center-screen">
  <div class="card">
    <h2>Реферальная программа</h2>
    <p class="muted">Чтобы получить реферальную ссылку и условия, напишите менеджеру <b>@Juilly</b>.</p>
    <div class="row">
      <button class="btn" onclick="go('home')">← В меню</button>
    </div>
  </div>
</section>

<!-- SUPPORT -->
<section id="support" class="center-screen">
  <div class="card">
    <h2>Поддержка</h2>
    <p class="muted">По всем вопросам обращайтесь к менеджеру <b>@Juilly</b>. Ответим максимально быстро.</p>
    <div class="row">
      <button class="btn" onclick="go('home')">← В меню</button>
    </div>
  </div>
</section>

<!-- CONTENT -->
<section id="content" class="center-screen">
  <div class="card">
    <div class="row">
      <h2 id="contentTitle">Информация</h2>
      <button class="btn" onclick="go('info')">← К функциям</button>
    </div>
    <div class="divider"></div>
    <div id="contentBody" style="text-align:left; line-height:1.6"></div>
  </div>
</section>

<script>
  // --- State & helpers ---
  const screens = ['start','lang','auth','home','profile','info','pay','ref','support','content'];
  const $ = sel => document.querySelector(sel);

  let state = {
    lang:'ru',
    user:null, // {username, pass, level}
    pendingPay:null // {planLevel, currency}
  };

  // base64 helpers
  const b64 = {
    enc: s => btoa(unescape(encodeURIComponent(s))),
    dec: s => decodeURIComponent(escape(atob(s)))
  };

  function buildKey(u){
    const payload = JSON.stringify({u:u.username, p:u.pass, l:u.level||0});
    return b64.enc(payload);
  }
  function parseKey(k){
    try{
      const o = JSON.parse(b64.dec(k));
      if(typeof o.u==='string' && typeof o.p==='string' && typeof o.l!=='undefined'){
        return {username:o.u, pass:o.p, level:o.l|0};
      }
    }catch(e){}
    return null;
  }
  function saveLocal(u){ localStorage.setItem('si_user', JSON.stringify(u)); }
  function loadLocal(){
    try{ return JSON.parse(localStorage.getItem('si_user')||'null'); }catch(e){ return null }
  }

  // --- Navigation ---
  function go(id){
    screens.forEach(s=>$('#'+s).classList.remove('active'));
    $('#'+id).classList.add('active');
    if(id==='info') updateEntitlements();
    if(id!=='profile') hideUserMenu();
  }
  function setLang(l){ state.lang=l; go('auth'); switchAuth('login'); }
  function switchAuth(which){
    $('#authLogin').style.display = which==='login' ? 'block':'none';
    $('#authRegister').style.display = which==='register' ? 'block':'none';
    $('#authKey').style.display = which==='key' ? 'block':'none';
  }

  // --- User badge ---
  function setBadge(u){
    if(u){
      $('#userBadge').style.display='inline-flex';
      $('#badgeName').textContent='@'+u.username;
    } else {
      $('#userBadge').style.display='none';
      hideUserMenu();
    }
  }
  function toggleUserMenu(){
    const m = $('#userMenu');
    m.style.display = (m.style.display==='flex' ? 'none' : 'flex');
  }
  function hideUserMenu(){ $('#userMenu').style.display='none'; }

  // --- Auth ---
  function register(){
    const username = $('#regUser').value.trim();
    const pass = $('#regPass').value.trim();
    if(!username || !pass){ alert('Введите username и пароль'); return; }
    state.user = {username, pass, level:0};
    saveLocal(state.user);
    setBadge(state.user);
    refreshProfile();
    go('home');
  }
  function login(){
    const username = $('#loginUser').value.trim();
    const pass = $('#loginPass').value.trim();
    const saved = loadLocal();
    if(saved && saved.username===username && saved.pass===pass){
      state.user = saved;
      setBadge(state.user);
      refreshProfile();
      go('home');
    } else {
      alert('Неверный логин/пароль или аккаунт ещё не сохранён на этом устройстве.\nЕсли у вас есть секретный ключ — используйте вход по ключу.');
    }
  }
  function loginByKey(){
    const key = $('#keyInput').value.trim();
    const obj = parseKey(key);
    if(!obj){ alert('Неверный ключ'); return; }
    state.user = {username:obj.username, pass:obj.pass, level:obj.level|0};
    saveLocal(state.user);
    setBadge(state.user);
    refreshProfile();
    go('home');
  }
  function logout(){
    state.user = null;
    saveLocal(null);
    setBadge(null);
    go('auth'); switchAuth('login');
  }
  function clearDevice(){
    localStorage.removeItem('si_user');
    alert('Данные на этом устройстве очищены.');
  }
  function refreshProfile(){
    const u = state.user;
    if(!u) return;
    $('#profileName').textContent='@'+u.username;
    $('#profileSub').textContent = subName(u.level);
    $('#profileKey').value = buildKey(u);
  }
  function copyKey(){
    $('#profileKey').select();
    document.execCommand('copy');
    $('#profileKey').blur();
    alert('Ключ скопирован. Используйте «Войти по ключу» на другом устройстве.');
  }
  function subName(lvl){
    if(lvl>=2) return 'Второй класс (вечная)';
    if(lvl>=1) return 'Первый класс (вечная)';
    return 'Нет';
  }

  // --- Entitlements & Info access ---
  function updateEntitlements(){
    const lvl = (state.user?.level)||0;
    setEntIcon('ent-1', true);
    setEntIcon('ent-2', lvl>=1);
    setEntIcon('ent-3', lvl>=2);
    setEntIcon('ent-4', lvl>=2);
    setEntIcon('ent-5', lvl>=2);
    hideUpsell();
  }
  function setEntIcon(id, ok){
    const el = $('#'+id);
    if(!el) return;
    el.textContent = ok ? '✓' : '✗';
    el.className = ok ? 'ok' : 'no';
  }

  function showUpsell(){ $('#upsell').style.display='block'; }
  function hideUpsell(){ $('#upsell').style.display='none'; }

  function requireLevel(want){
    const have = (state.user?.level)||0;
    return have>=want;
  }

  function openInfo(level){
    if(!state.user){ alert('Сначала войдите.'); go('auth'); return; }
    const needs = [0,0,1,2,2,2]; // index by level: 1..5
    const need = needs[level]||0;
    if(!requireLevel(need)){
      showUpsell();
      return;
    }
    showContent(level);
  }

  function showContent(level){
    const titleEl = $('#contentTitle');
    const bodyEl = $('#contentBody');

    if(level===1){
      titleEl.textContent = '📅 Дата выхода Telegram-подарков';
      bodyEl.innerHTML = `
        <div class="notice">Окно следующей волны: <b>20 августа 2025</b> ± 48 часов (индикативно).</div>
        <h3>Что подготовить заранее</h3>
        <ul>
          <li>Баланс в выбранной валюте (звёзды/рубли/$) + резерв</li>
          <li>Устройство → сеть → прокси (при необходимости)</li>
          <li>Алгоритм действий на первые 5 минут после старта</li>
        </ul>
        <h3>Сигналы к запуску</h3>
        <ul>
          <li>Изменения в ответах API каталога подарков</li>
          <li>Появление новых превью-иконок/метаданных</li>
          <li>Аномалии мониторинга (всплески 5–10 мин до старта)</li>
        </ul>`;
    }

    if(level===2){
      titleEl.textContent = '📰 NFT-каналы + звонящие боты';
      bodyEl.innerHTML = `
        <div class="notice">Отбор по метрикам сигнала/шума и средней задержке публикаций.</div>
        <h3>Каналы наблюдения</h3>
        <ul>
          <li><b>@nft_news</b> — анонсы и дайджесты с низким спамом</li>
          <li><b>@crypto_trends</b> — макро-сводки и редкие коллекции</li>
          <li><b>@rare_drop_watch</b> — фильтрованные дроп-снапшоты</li>
        </ul>
        <h3>Звонящие боты</h3>
        <ul>
          <li><b>@gift_alert_bot</b> — триггеры по ключевым событиям → звонок</li>
          <li><b>@drop_call_me_bot</b> — резервный сценарий</li>
        </ul>
        <h3>Лучшие практики</h3>
        <ul>
          <li>Разделите номера: основной / резервный</li>
          <li>Quiet hours + приоритетные контакты, чтобы не пропустить</li>
          <li>Интеграция с календарём релизов (iCal)</li>
        </ul>`;
    }

    if(level===3){
      titleEl.textContent = '🤖 Автоскупка + скрипты';
      bodyEl.innerHTML = `
        <div class="notice">Фокус на скорость: пред-подписи, ретраи, минимизация кликов.</div>
        <h3>Боты автоскупки</h3>
        <ul>
          <li><b>@auto_gift_bot</b> — очередь заявок, приоритет горячих позиций</li>
          <li><b>@fast_sweep_bot</b> — агрессивный режим, следите за лимитами</li>
        </ul>
        <h3>Скрипты</h3>
        <ul>
          <li><code>gift-buy.js</code> — эмуляция кликов, prefetch, backoff</li>
          <li><code>telegram-gift-sniper.py</code> — API-слоты, тайминги, логирование</li>
        </ul>
        <h3>Инфраструктура</h3>
        <ul>
          <li>Выделенные прокси под горячие покупки</li>
          <li>Разнесение платёжных источников</li>
          <li>Лимиты расходов и детальные логи</li>
        </ul>`;
    }

    if(level===4){
      titleEl.textContent = '👑 Премиум-функции';
      bodyEl.innerHTML = `
        <div class="notice">Только для «Второго класса». Модуль расширяется.</div>
        <h3>Что включено</h3>
        <ul>
          <li>Мгновенные алерты: push + звонок + email</li>
          <li>Приоритетные каналы с приватными анонсами</li>
          <li>Бета-фичи: предпросмотр карточек, автозаполнение</li>
        </ul>
        <h3>Рекомендации</h3>
        <ul>
          <li>Дублируйте контур оповещений (основной/резерв)</li>
          <li>Храните токены/ключи в менеджере паролей</li>
        </ul>`;
    }

    if(level===5){
      titleEl.textContent = '🧩 Скрипт от автора';
      bodyEl.innerHTML = `
        <div class="notice">Эксклюзив для «Второго класса». Помогу адаптировать под вашу систему.</div>
        <h3>Что вы получите</h3>
        <ul>
          <li>Базовый шаблон скрипта (JS/Python на выбор)</li>
          <li>Комментарии по интеграции и настройкам</li>
          <li>Подсказки по оптимизации таймингов</li>
        </ul>
        <h3>Стартовый каркас (пример)</h3>
<pre style="background:#0d0d14;padding:12px;border-radius:8px;overflow:auto;"><code>// gift-sniper.template.js
import { performance } from 'perf_hooks';

async function snipe({ endpoint, auth, targetId }) {
  const start = performance.now();
  // 1) prefetch metadata
  // 2) prepare signed request
  // 3) retry with exponential backoff
  // TODO: вставим реальные шаги под вашу инфраструктуру
  return { ok:true, elapsed: performance.now()-start };
}
export default snipe;</code></pre>
        <p class="muted">Готов обсудить детали и собрать финальный вариант вместе.</p>`;
    }

    go('content');
  }

  // --- Purchase flow ---
  function choosePlan(level, currency){
    state.pendingPay = {planLevel:level, currency};
    const text = (level===2?'Второй класс':'Первый класс') + ' • ' +
      (currency==='stars'?'⭐ Звёзды':currency==='rub'?'₽ Рубли':'$ Доллары');
    $('#paySummary').textContent = text;
    $('#payConfirm').style.display='block';
  }
  function cancelPay(){ state.pendingPay=null; $('#payConfirm').style.display='none'; }
  function doPay(){
    if(!state.user){ alert('Сначала войдите или зарегистрируйтесь.'); go('auth'); return; }
    if(!state.pendingPay){ alert('Выберите подписку и способ оплаты.'); return; }
    const lvl = state.pendingPay.planLevel; // 1 или 2
    state.user.level = Math.max(state.user.level||0, lvl);
    saveLocal(state.user);
    refreshProfile();
    $('#payConfirm').style.display='none';
    alert('Оплата успешна. Активирована подписка: ' + subName(state.user.level));
    updateEntitlements();
    go('info');
  }

  // --- Boot ---
  (function boot(){
    const saved = loadLocal();
    if(saved){ state.user=saved; setBadge(saved); refreshProfile(); go('home'); }
  })();

  // close user menu on outside click
  window.addEventListener('click', (e)=>{
    const m = $('#userMenu'); const b = $('#userBadge');
    if(!m) return;
    if(m.style.display==='flex' && !m.contains(e.target) && !b.contains(e.target)){
      hideUserMenu();
    }
  });
</script>
</body>
</html>
