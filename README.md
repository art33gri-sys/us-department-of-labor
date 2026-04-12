<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>U.S. Department of Labor | Отчетность</title>
    <style>
        :root {
            --primary: #002e6d;
            --secondary: #205493;
            --accent: #cd2026;
            --light: #f9f9f9;
            --gold: #f1c40f;
        }

        body { font-family: 'Segoe UI', Arial, sans-serif; margin: 0; background: #e9ecef; color: #333; }
        
        /* Header */
        header { background: var(--primary); color: white; padding: 15px 0; border-bottom: 4px solid var(--accent); }
        .container { max-width: 1000px; margin: 0 auto; padding: 0 20px; }
        .header-flex { display: flex; align-items: center; justify-content: space-between; }
        .logo-box { display: flex; align-items: center; gap: 15px; }
        .logo-box img { width: 80px; height: 80px; filter: brightness(0) invert(1); }
        
        /* Nav */
        nav { background: var(--secondary); padding: 10px 0; display: flex; justify-content: space-between; align-items: center; }
        nav a { color: white; text-decoration: none; font-weight: bold; margin-right: 20px; font-size: 13px; }
        .admin-btn { background: var(--accent); padding: 5px 15px; border-radius: 3px; cursor: pointer; border: none; color: white; }

        /* Content */
        .card { background: white; margin: 20px 0; padding: 25px; border-radius: 2px; box-shadow: 0 2px 5px rgba(0,0,0,0.1); position: relative; }
        h2 { color: var(--primary); border-bottom: 2px solid var(--primary); padding-bottom: 10px; margin-top: 0; font-size: 20px; }
        
        .edit-area { width: 100%; min-height: 100px; display: none; margin-top: 10px; font-family: monospace; border: 1px solid #ccc; padding: 10px; }
        .btn-save { display: none; background: #27ae60; color: white; border: none; padding: 8px 15px; cursor: pointer; margin-top: 10px; }
        .edit-trigger { display: none; position: absolute; top: 20px; right: 20px; background: var(--secondary); color: white; border: none; padding: 5px 10px; cursor: pointer; font-size: 12px; }

        .stats { display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; margin: 15px 0; }
        .stat-card { background: var(--light); padding: 10px; border-left: 3px solid var(--accent); }
        .stat-card b { font-size: 18px; color: var(--primary); }

        /* Login Modal */
        #loginModal { display: none; position: fixed; top: 50%; left: 50%; transform: translate(-50%, -50%); background: white; padding: 30px; box-shadow: 0 0 20px rgba(0,0,0,0.5); z-index: 100; border-top: 5px solid var(--primary); }
        #loginModal input { display: block; width: 100%; margin: 10px 0; padding: 8px; box-sizing: border-box; }
        .overlay { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.7); z-index: 99; }
    </style>
</head>
<body>

<header>
    <div class="container header-flex">
        <div class="logo-box">
            <img src="https://i.ibb.co/VWVm0Qz/dol-logo.png" alt="DOL Logo" id="mainLogo"> 
            <div>
                <h1 style="margin:0; font-size: 22px;">U.S. DEPARTMENT OF LABOR</h1>
                <small>OFFICIAL SECRETARY OF LABOR PORTAL</small>
            </div>
        </div>
        <div style="text-align: right;">
            <div id="userStatus">Гость</div>
        </div>
    </div>
</header>

<nav>
    <div class="container" style="display: flex; justify-content: space-between; width: 100%;">
        <div>
            <a href="#">ГЛАВНАЯ</a>
            <a href="#">АРХИВ ОТЧЕТОВ</a>
            <a href="#">СОТРУДНИКИ</a>
        </div>
        <button class="admin-btn" onclick="toggleLogin()">ВХОД В СИСТЕМУ</button>
    </div>
</nav>

<div class="container">
    <div class="card" id="block1">
        <button class="edit-trigger" onclick="enableEdit('block1')">РЕДАКТИРОВАТЬ</button>
        <h2>Состав Министерства Труда</h2>
        <div class="content-view">
            <p>Общий состав: <b>26 человек</b></p>
            <ul>
                <li>Министр Artur Drovic: 34%</li>
                <li>Зам. Министра Miy Li: 33%</li>
                <li>Зам. Министра Elmer Moroz: 33%</li>
            </ul>
        </div>
        <textarea class="edit-area"></textarea>
        <button class="btn-save" onclick="saveEdit('block1')">СОХРАНИТЬ В АРХИВ</button>
    </div>

    <div class="card" id="block2">
        <button class="edit-trigger" onclick="enableEdit('block2')">РЕДАКТИРОВАТЬ</button>
        <h2>Отчет Аппарата Правительства (Weekly)</h2>
        <div class="content-view">
            <div class="stats">
                <div class="stat-card">Лицензии: <b>315</b></div>
                <div class="stat-card">Оружие: <b>239</b></div>
                <div class="stat-card">Собесы: <b>6</b></div>
            </div>
            <p>Глава Аппарата: Mops Sergeyevich (60%)<br>Зам. Главы: Jamess Soprano (40%)</p>
        </div>
        <textarea class="edit-area"></textarea>
        <button class="btn-save" onclick="saveEdit('block2')">СОХРАНИТЬ В АРХИВ</button>
    </div>

    <div class="card" id="block3">
        <button class="edit-trigger" onclick="enableEdit('block3')">РЕДАКТИРОВАТЬ</button>
        <h2>Департамент Здравоохранения</h2>
        <div class="content-view">
            <p>Штрафы: <b>280.000$</b> | Акции: <b>101.000$</b></p>
            <p>Премии: Dia Dema (50%), Dis Morales (40%), Luigi Cudi (10%)</p>
        </div>
        <textarea class="edit-area"></textarea>
        <button class="btn-save" onclick="saveEdit('block3')">СОХРАНИТЬ В АРХИВ</button>
    </div>
</div>

<div class="overlay" id="overlay" onclick="toggleLogin()"></div>
<div id="loginModal">
    <h3>Авторизация</h3>
    <input type="text" id="username" placeholder="Логин (admin)">
    <input type="password" id="password" placeholder="Пароль (1234)">
    <button class="admin-btn" style="width: 100%;" onclick="login()">ВОЙТИ</button>
</div>

<script>
    // Загрузка данных из "архива" (LocalStorage)
    window.onload = function() {
        ['block1', 'block2', 'block3'].forEach(id => {
            const saved = localStorage.getItem(id);
            if (saved) document.querySelector(`#${id} .content-view`).innerHTML = saved;
        });
    }

    function toggleLogin() {
        const display = document.getElementById('loginModal').style.display;
        document.getElementById('loginModal').style.display = display === 'block' ? 'none' : 'block';
        document.getElementById('overlay').style.display = display === 'block' ? 'none' : 'block';
    }

    function login() {
        const u = document.getElementById('username').value;
        const p = document.getElementById('password').value;
        if(u === 'admin' && p === '1234') {
            document.getElementById('userStatus').innerHTML = 'Администратор: <b>Artur Drovic</b>';
            document.querySelectorAll('.edit-trigger').forEach(el => el.style.display = 'block');
            toggleLogin();
            alert('Доступ разрешен. Теперь вы можете менять отчеты.');
        } else {
            alert('Ошибка доступа!');
        }
    }

    function enableEdit(blockId) {
        const block = document.getElementById(blockId);
        const view = block.querySelector('.content-view');
        const area = block.querySelector('.edit-area');
        const saveBtn = block.querySelector('.btn-save');

        area.value = view.innerHTML.trim();
        view.style.display = 'none';
        area.style.display = 'block';
        saveBtn.style.display = 'block';
    }

    function saveEdit(blockId) {
        const block = document.getElementById(blockId);
        const view = block.querySelector('.content-view');
        const area = block.querySelector('.edit-area');
        const saveBtn = block.querySelector('.btn-save');

        const newContent = area.value;
        view.innerHTML = newContent;
        localStorage.setItem(blockId, newContent); // Сохраняем в "архив" браузера

        view.style.display = 'block';
        area.style.display = 'none';
        saveBtn.style.display = 'none';
        alert('Изменения сохранены в архив системы.');
    }
</script>

</body>
</html>
