<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>U.S. Department of Labor | Official Portal</title>
    
    <style>
        :root {
            --primary: #002e6d;
            --secondary: #205493;
            --accent: #cd2026;
            --text: #323a45;
            --border: #d6d7d9;
        }

        body { font-family: 'Georgia', serif; margin: 0; background-color: #f0f0f0; color: var(--text); }
        
        /* Шапка */
        header { background: var(--primary); color: white; padding: 25px 0; border-bottom: 4px solid var(--accent); }
        .container { max-width: 1000px; margin: 0 auto; padding: 0 20px; }
        .header-flex { display: flex; align-items: center; justify-content: space-between; }
        .logo-area { display: flex; align-items: center; gap: 20px; }
        .logo-area img { width: 85px; filter: brightness(0) invert(1); }
        
        /* Навигация */
        nav { background: var(--secondary); padding: 12px 0; box-shadow: 0 2px 5px rgba(0,0,0,0.1); }
        .nav-flex { display: flex; justify-content: space-between; align-items: center; }
        .nav-links a { color: white; text-decoration: none; font-weight: bold; font-size: 13px; margin-right: 25px; font-family: sans-serif; }
        
        .login-trigger { background: transparent; border: 1px solid white; color: white; padding: 5px 15px; cursor: pointer; border-radius: 2px; transition: 0.3s; }
        .login-trigger:hover { background: white; color: var(--secondary); }

        /* Контент отчета */
        main { padding: 40px 0; }
        .report-paper { background: white; padding: 60px; box-shadow: 0 0 15px rgba(0,0,0,0.1); border: 1px solid var(--border); position: relative; }
        
        .official-seal { position: absolute; top: 40px; right: 60px; width: 120px; opacity: 0.15; pointer-events: none; }
        
        h2 { border-bottom: 2px solid var(--primary); padding-bottom: 10px; color: var(--primary); text-transform: uppercase; font-size: 22px; }
        h3 { background: #f4f4f4; padding: 10px; border-left: 5px solid var(--primary); font-size: 18px; margin-top: 30px; }

        .data-table { width: 100%; border-collapse: collapse; margin: 20px 0; }
        .data-table td { padding: 12px; border-bottom: 1px solid #eee; }
        .data-table .label { font-weight: bold; width: 45%; color: #555; }
        .badge { background: var(--primary); color: white; padding: 3px 8px; border-radius: 3px; font-size: 12px; font-family: sans-serif; }

        /* Окно входа */
        .modal-overlay { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.85); z-index: 1000; align-items: center; justify-content: center; }
        .login-box { background: white; width: 350px; padding: 30px; border-top: 6px solid var(--primary); position: relative; }
        .login-box h4 { margin: 0 0 20px 0; color: var(--primary); text-align: center; font-family: sans-serif; }
        .login-box input { width: 100%; padding: 12px; margin-bottom: 15px; border: 1px solid #ccc; box-sizing: border-box; }
        .btn-auth { width: 100%; padding: 12px; background: var(--primary); color: white; border: none; cursor: pointer; font-weight: bold; }

        /* Системная панель после входа */
        #system-panel { display: none; background: #fff3cd; padding: 15px; border: 1px solid #ffeeba; margin-bottom: 20px; text-align: center; font-family: sans-serif; }
        .edit-btn { background: var(--accent); color: white; border: none; padding: 5px 15px; cursor: pointer; border-radius: 3px; font-weight: bold; margin-left: 10px; }

        footer { text-align: center; padding: 40px; color: #777; font-size: 12px; border-top: 1px solid #ddd; margin-top: 50px; font-family: sans-serif; }
    </style>
</head>
<body>

<header>
    <div class="container header-flex">
        <div class="logo-area">
            <img src="https://i.ibb.co/VWVm0Qz/dol-logo.png" alt="Seal">
            <div>
                <h1 style="margin:0; font-size: 26px; letter-spacing: 1px;">UNITED STATES DEPARTMENT OF LABOR</h1>
                <p style="margin:5px 0 0 0; opacity: 0.8; font-family: sans-serif; font-size: 12px;">OFFICIAL REPORTING SYSTEM | EST. 1913</p>
            </div>
        </div>
    </div>
</header>

<nav>
    <div class="container nav-flex">
        <div class="nav-links">
            <a href="#">ГЛАВНАЯ</a>
            <a href="#">ПУБЛИЧНЫЙ АРХИВ</a>
            <a href="#">ДЕПАРТАМЕНТЫ</a>
        </div>
        <button class="login-trigger" id="loginBtn" onclick="toggleModal(true)">ВХОД В СИСТЕМУ</button>
    </div>
</nav>

<main class="container">
    <div id="system-panel">
        <span id="user-info">Панель активирована.</span> 
        <button class="edit-btn" id="edit-mode-btn" style="display:none;" onclick="alert('Режим редактирования базы данных открыт.')">РЕДАКТИРОВАТЬ ОТЧЕТ</button>
    </div>

    <div class="report-paper" id="main-report">
        <img src="https://i.ibb.co/VWVm0Qz/dol-logo.png" class="official-seal">
        
        <p style="text-align: right; font-style: italic;">Экземпляр №1<br>Дата формирования: 12.05.2026</p>
        
        <div style="text-align: center; margin-bottom: 50px;">
            <h2 style="border: none;">Еженедельный отчет Министерства Труда</h2>
            <p>Период: <b>05.04.2026 — 12.05.2026</b></p>
            <p>Получатель: <b>Губернатор Anna Moroz</b></p>
            <p>Подающий: <b>Министр Труда Artur Drovic</b></p>
        </div>

        <h3>1. Кадровый аудит</h3>
        <table class="data-table">
            <tr><td class="label">Общий штат Министерства:</td><td>26 сотрудников</td></tr>
            <tr><td class="label">Министр Труда:</td><td>Artur Drovic <span class="badge">34%</span></td></tr>
            <tr><td class="label">Зам. Министра:</td><td>Miy Li <span class="badge">33%</span></td></tr>
            <tr><td class="label">Зам. Министра:</td><td>Elmer Moroz <span class="badge">33%</span></td></tr>
        </table>

        <h3>2. Деятельность Аппарата Правительства</h3>
        <p>Кадровый состав: 16 сотрудников</p>
        <table class="data-table">
            <tr><td class="label">Выдано лицензий (Всего):</td><td><b>315</b></td></tr>
            <tr><td class="label">Лицензии на оружие:</td><td>239</td></tr>
            <tr><td class="label">Охота / Рыбалка:</td><td>33 / 33</td></tr>
            <tr><td class="label">Проведено собеседований:</td><td>6</td></tr>
            <tr><td class="label">Принято на стажировку:</td><td>24 (Открытые) / 23 (Закрытые)</td></tr>
            <tr><td class="label">Экзамены / Лекции:</td><td>40 / 33</td></tr>
            <tr><td class="label">Произведено переводов:</td><td>6</td></tr>
        </table>
        <p><b>Премирование Аппарата:</b> Глава Mops Sergeyevich (60%), Зам. Главы Jamess Soprano (40%)</p>

        <h3>3. Департамент Здравоохранения</h3>
        <p>Кадровый состав: 7 сотрудников</p>
        <table class="data-table">
            <tr><td class="label">Надзорные мероприятия:</td><td>22</td></tr>
            <tr><td class="label">Присутствие на мед. проверках:</td><td>4</td></tr>
            <tr><td class="label">Собеседования в EMS:</td><td>6</td></tr>
            <tr><td class="label">Выявлено нарушений сотрудников EMS:</td><td><b>0</b></td></tr>
            <tr><td class="label">Благотворительные акции:</td><td>3</td></tr>
            <tr><td class="label">Сумма вознаграждений за акции:</td><td><span style="color: green; font-weight: bold;">101.000$</span></td></tr>
            <tr><td class="label">Общая сумма штрафов:</td><td><span style="color: var(--accent); font-weight: bold;">280.000$</span></td></tr>
        </table>
        <p><b>Премирование:</b> Директор Dia Dema (50%), Зам. Директора Dis Morales (40%), Зам. Директора Luigi Cudi (10%)</p>

        <div style="margin-top: 50px; border-top: 1px solid #eee; padding-top: 20px;">
            <p>Прикрепленные документы:<br>
            📄 Еженедельный отчет Главы Аппарата Правительства от 05.04.26<br>
            📄 Еженедельный отчет Инспектора Департамента Здравоохранения от 05.04.26</p>
            <p style="margin-top: 30px;">Подпись: ____________________ / A. Drovic /</p>
        </div>
    </div>
</main>

<div class="modal-overlay" id="loginModal">
    <div class="login-box">
        <button onclick="toggleModal(false)" style="position:absolute; right:10px; top:10px; border:none; background:none; cursor:pointer;">✕</button>
        <h4>System Authorization</h4>
        <input type="email" id="email" placeholder="minister@labor.gov.us">
        <input type="password" id="password" placeholder="Password">
        <button class="btn-auth" onclick="handleLogin()">ACCESS SYSTEM</button>
    </div>
</div>

<footer>
    <div class="container">
        <p>U.S. Department of Labor | 200 Constitution Ave NW, Washington, DC 20210</p>
        <p>This is a secure government network. Unauthorized access is prohibited.</p>
    </div>
</footer>

<script>
    // Встроенная база пользователей (для моментальной работы)
    const systemUsers = {
        "minister@labor.gov.us": { pass: "Admin2026", role: "Министр", name: "Artur Drovic", canEdit: true },
        "deputy1@labor.gov.us": { pass: "Dep2026", role: "Заместитель", name: "Miy Li", canEdit: false },
        "deputy2@labor.gov.us": { pass: "Dep2026", role: "Заместитель", name: "Elmer Moroz", canEdit: false }
    };

    let currentUser = null;

    function toggleModal(show) {
        document.getElementById('loginModal').style.display = show ? 'flex' : 'none';
    }

    function handleLogin() {
        const email = document.getElementById('email').value.trim();
        const pass = document.getElementById('password').value.trim();
        
        if (systemUsers[email] && systemUsers[email].pass === pass) {
            currentUser = systemUsers[email];
            
            // Настройка панели после успешного входа
            document.getElementById('system-panel').style.display = 'block';
            document.getElementById('user-info').innerHTML = `Авторизация: <b>${currentUser.role} ${currentUser.name}</b>.`;
            document.getElementById('loginBtn').innerText = 'ВЫЙТИ';
            
            // Если это Министр, показываем кнопку редактирования
            if (currentUser.canEdit) {
                document.getElementById('edit-mode-btn').style.display = 'inline-block';
            } else {
                document.getElementById('edit-mode-btn').style.display = 'none';
            }

            toggleModal(false);
            alert(`Доступ разрешен. Добро пожаловать, ${currentUser.name}.`);
        } else {
            alert('Ошибка доступа: Неверный логин или пароль .gov');
        }
    }
</script>

</body>
</html>
