<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>U.S. Department of Labor | Management System</title>
    <style>
        :root { --primary: #002e6d; --accent: #cd2026; --gold: #b69156; --bg: #f8f9fa; }
        body { font-family: 'Times New Roman', serif; margin: 0; background: var(--bg); color: #333; }
        
        header { background: var(--primary); color: white; padding: 20px 0; border-bottom: 4px solid var(--accent); }
        .container { max-width: 1100px; margin: 0 auto; padding: 0 20px; }
        .header-content { display: flex; align-items: center; justify-content: space-between; }
        .logo-box { display: flex; align-items: center; gap: 20px; }
        .logo-box img { width: 80px; height: 80px; }

        nav { background: #112e51; padding: 10px 0; display: flex; justify-content: center; gap: 10px; }
        .nav-btn { 
            text-decoration: none; color: white; font-weight: bold; padding: 8px 20px; 
            border: 2px solid var(--gold); border-radius: 4px; font-family: sans-serif; cursor: pointer;
        }
        .nav-btn:hover { background: var(--gold); color: black; }

        .report-paper { background: white; padding: 50px; margin: 30px auto; box-shadow: 0 0 20px rgba(0,0,0,0.1); border: 1px solid #ccc; position: relative; }
        h2 { text-align: center; color: var(--primary); border-bottom: 2px solid var(--primary); }
        h3 { background: #f1f1f1; padding: 10px; border-left: 5px solid var(--primary); color: var(--primary); }

        table { width: 100%; border-collapse: collapse; margin: 10px 0; }
        table th, table td { padding: 10px; border: 1px solid #ddd; text-align: left; }
        table th { background: #f4f4f4; }
        .pct-box { background: var(--primary); color: white; padding: 2px 6px; border-radius: 3px; font-size: 13px; font-family: sans-serif; }

        /* Админ-панели */
        .admin-panel { display: none; background: #fff; border: 2px solid var(--gold); padding: 20px; margin-top: 20px; border-radius: 8px; font-family: sans-serif; }
        input, textarea, select { width: 100%; padding: 8px; margin: 5px 0 10px; box-sizing: border-box; }
        .btn-action { background: #28a745; color: white; border: none; padding: 10px; cursor: pointer; width: 100%; font-weight: bold; }
        .btn-del { background: var(--accent); margin-top: 5px; }

        .log-item { font-size: 12px; border-bottom: 1px solid #eee; padding: 5px; font-family: monospace; }
        .tab-content { display: none; }
        .active { display: block; }
    </style>
</head>
<body>

<header>
    <div class="container header-content">
        <div class="logo-box">
            <img src="https://upload.wikimedia.org/wikipedia/commons/d/da/Seal_of_the_United_States_Department_of_Labor.svg" alt="Seal">
            <div><h1 style="margin:0;">U.S. DEPARTMENT OF LABOR</h1><p style="margin:0;">Official Management & Reporting System</p></div>
        </div>
        <div id="user-display" style="text-align:right; font-family:sans-serif; font-size:14px; color: #gold;"></div>
    </div>
</header>

<nav>
    <div class="nav-btn" onclick="switchTab('main')">ГЛАВНАЯ</div>
    <div class="nav-btn" onclick="switchTab('archive')">АРХИВ</div>
    <div class="nav-btn" id="nav-editor" style="display:none;" onclick="switchTab('editor')">РЕДАКТОР</div>
    <div class="nav-btn" id="nav-users" style="display:none;" onclick="switchTab('users')">ПЕРСОНАЛ</div>
    <div class="nav-btn" id="nav-logs" style="display:none;" onclick="switchTab('logs')">ЛОГИ</div>
    <div class="nav-btn" style="border-color:#5cb85c" onclick="document.getElementById('loginModal').style.display='block'">ВХОД</div>
</nav>

<div class="container">

    <div id="tab-main" class="tab-content active">
        <div class="report-paper" id="report-view"></div>
    </div>

    <div id="tab-archive" class="tab-content">
        <h2 style="margin-top:40px;">Архив департамента</h2>
        <div id="archive-list"></div>
    </div>

    <div id="tab-editor" class="tab-content">
        <div class="admin-panel">
            <h3>Редактирование текущего отчета</h3>
            <label>Период:</label><input type="text" id="ed-period">
            <label>Контент (HTML):</label><textarea id="ed-content" rows="15"></textarea>
            <label>Документы (Имя|Ссылка):</label><textarea id="ed-links" rows="3"></textarea>
            <button class="btn-action" onclick="saveReport()">ОПУБЛИКОВАТЬ</button>
        </div>
    </div>

    <div id="tab-users" class="tab-content">
        <div class="admin-panel">
            <h3>Управление персоналом</h3>
            <div id="users-list"></div>
            <hr>
            <h4>Добавить сотрудника</h4>
            <input type="email" id="new-email" placeholder="Email">
            <input type="text" id="new-name" placeholder="Имя Фамилия">
            <input type="password" id="new-pass" placeholder="Пароль">
            <select id="new-role"><option value="Admin">Министр (Admin)</option><option value="Editor">Заместитель (Editor)</option></select>
            <button class="btn-action" onclick="addUser()">СОЗДАТЬ АККАУНТ</button>
        </div>
    </div>

    <div id="tab-logs" class="tab-content">
        <div class="admin-panel" style="max-height: 500px; overflow-y: auto;">
            <h3>Системные логи</h3>
            <div id="logs-container"></div>
            <button class="btn-action btn-del" onclick="clearLogs()">ОЧИСТИТЬ ЛОГИ</button>
        </div>
    </div>

</div>

<div id="loginModal" style="display:none; position:fixed; top:50%; left:50%; transform:translate(-50%,-50%); background:white; padding:30px; border-top:8px solid var(--primary); z-index:1000; box-shadow:0 0 100px rgba(0,0,0,0.5); font-family:sans-serif;">
    <h3>System Access</h3>
    <input type="email" id="log-e" placeholder="Email">
    <input type="password" id="log-p" placeholder="Password">
    <button class="btn-action" onclick="login()">ENTER</button>
    <button onclick="this.parentElement.style.display='none'" style="border:none; background:none; cursor:pointer; margin-top:10px; color:gray;">Закрыть</button>
</div>

<script>
    // --- ИНИЦИАЛИЗАЦИЯ БАЗЫ ---
    let users = JSON.parse(localStorage.getItem('dol_users')) || [
        {email: 'minister@dol.gov', pass: 'Laba2026', name: 'Artur Drovic', role: 'Admin'},
        {email: 'miy.li@dol.gov', pass: 'Gov2026', name: 'Miy Li', role: 'Editor'},
        {email: 'elmer.moroz@dol.gov', pass: 'Gov2026', name: 'Elmer Moroz', role: 'Editor'}
    ];

    let logs = JSON.parse(localStorage.getItem('dol_logs')) || [];
    let reports = JSON.parse(localStorage.getItem('dol_reports')) || [{
        meta: "Экземпляр №1 | Дата: 12.05.2026",
        period: "05.04.2026 — 12.05.2026",
        content: `<h3>1. Кадровый состав Министерства</h3>
<table><tr><th>Должность</th><th>Имя Фамилия</th><th>Премия</th></tr>
<tr><td>Министр Труда</td><td>Artur Drovic</td><td><span class="pct-box">34%</span></td></tr>
<tr><td>Зам. Министра</td><td>Miy Li</td><td><span class="pct-box">33%</span></td></tr>
<tr><td>Зам. Министра</td><td>Elmer Moroz</td><td><span class="pct-box">33%</span></td></tr></table>

<h3>2. Аппарат Правительства</h3>
<table><tr><td>Выдано лицензий</td><td><b>315</b></td></tr><tr><td>Оружие/Охота/Рыбалка</td><td>239 / 33 / 33</td></tr><tr><td>Стажировки</td><td>47 человек</td></tr></table>
<h4>Список на премирование (Аппарат)</h4>
<table><tr><th>Сотрудник</th><th>Доля</th></tr>
<tr><td>Mops Sergeyevich (Глава)</td><td><span class="pct-box">60%</span></td></tr>
<tr><td>Jamess Soprano (Зам)</td><td><span class="pct-box">40%</span></td></tr></table>

<h3>3. Департамент Здравоохранения</h3>
<table><tr><td>Штрафы / Акции</td><td><b>280.000$ / 101.000$</b></td></tr><tr><td>Мед. проверки / Собесы EMS</td><td>4 / 6</td></tr><tr><td>Нарушения в EMS</td><td><b>0</b></td></tr></table>
<h4>Список на премирование (ДЗ)</h4>
<table><tr><th>Сотрудник</th><th>Доля</th></tr>
<tr><td>Dia Dema (Директор)</td><td><span class="pct-box">50%</span></td></tr>
<tr><td>Dis Morales (Зам)</td><td><span class="pct-box">40%</span></td></tr>
<tr><td>Luigi Cudi (Зам)</td><td><span class="pct-box">10%</span></td></tr></table>`,
        links: "Отчет Аппарата|#\nОтчет Здравоохранения|#"
    }];

    let currentUser = null;

    function addLog(msg) {
        let entry = `[${new Date().toLocaleString()}] ${currentUser ? currentUser.name : 'Гость'}: ${msg}`;
        logs.unshift(entry);
        localStorage.setItem('dol_logs', JSON.stringify(logs.slice(0, 50)));
        renderLogs();
    }

    function login() {
        let e = document.getElementById('log-e').value;
        let p = document.getElementById('log-p').value;
        let u = users.find(x => x.email === e && x.pass === p);
        if(u) {
            currentUser = u;
            document.getElementById('user-display').innerHTML = `User: <b>${u.name}</b> [${u.role}]`;
            document.getElementById('nav-editor').style.display = 'block';
            if(u.role === 'Admin') {
                document.getElementById('nav-users').style.display = 'block';
                document.getElementById('nav-logs').style.display = 'block';
            }
            document.getElementById('loginModal').style.display = 'none';
            addLog("Авторизация в системе");
            switchTab('main');
        } else { alert("Ошибка доступа"); }
    }

    function saveReport() {
        let newR = {
            meta: `Экземпляр №${reports.length + 1} | Дата: ${new Date().toLocaleDateString()}`,
            period: document.getElementById('ed-period').value,
            content: document.getElementById('ed-content').value,
            links: document.getElementById('ed-links').value
        };
        reports.unshift(newR);
        localStorage.setItem('dol_reports', JSON.stringify(reports));
        addLog(`Опубликован новый отчет за период ${newR.period}`);
        location.reload();
    }

    function addUser() {
        let u = {
            email: document.getElementById('new-email').value,
            name: document.getElementById('new-name').value,
            pass: document.getElementById('new-pass').value,
            role: document.getElementById('new-role').value
        };
        users.push(u);
        localStorage.setItem('dol_users', JSON.stringify(users));
        addLog(`Добавлен новый пользователь: ${u.name}`);
        renderUsers();
    }

    function deleteUser(idx) {
        if(confirm("Удалить сотрудника?")) {
            addLog(`Удален пользователь: ${users[idx].name}`);
            users.splice(idx, 1);
            localStorage.setItem('dol_users', JSON.stringify(users));
            renderUsers();
        }
    }

    function renderUsers() {
        let h = '';
        users.forEach((u, i) => {
            h += `<div style="padding:10px; border-bottom:1px solid #ddd;">${u.name} (${u.email}) - <b>${u.role}</b> 
                  ${u.email !== 'minister@dol.gov' ? `<button class="btn-del" onclick="deleteUser(${i})">Удалить</button>` : ''}</div>`;
        });
        document.getElementById('users-list').innerHTML = h;
    }

    function renderLogs() {
        document.getElementById('logs-container').innerHTML = logs.map(l => `<div class="log-item">${l}</div>`).join('');
    }

    function switchTab(t) {
        document.querySelectorAll('.tab-content').forEach(x => x.classList.remove('active'));
        document.getElementById('tab-' + t).classList.add('active');
        if(t === 'users') renderUsers();
        if(t === 'logs') renderLogs();
        if(t === 'archive') renderArchive();
        if(t === 'editor') {
            document.getElementById('ed-period').value = reports[0].period;
            document.getElementById('ed-content').value = reports[0].content;
            document.getElementById('ed-links').value = reports[0].links;
        }
    }

    function renderReportView(r) {
        let links = r.links.split('\n').map(l => {
            let [n, url] = l.split('|');
            return `<a href="${url}" style="color:blue; display:block;">📄 ${n}</a>`;
        }).join('');
        document.getElementById('report-view').innerHTML = `<div style="font-style:italic; color:gray; margin-bottom:10px;">${r.meta}</div>
        <h2>Еженедельный отчет Министерства Труда</h2><p style="text-align:center;">Период: <b>${r.period}</b></p>${r.content}<h3>Прикрепленные документы</h3>${links}`;
    }

    function renderArchive() {
        document.getElementById('archive-list').innerHTML = reports.map((r, i) => 
            `<div style="background:white; padding:15px; margin-bottom:10px; border:1px solid #ddd; cursor:pointer;" onclick="renderReportView(reports[${i}]); switchTab('main');">
            <b>${r.period}</b><br><small>${r.meta}</small></div>`).join('');
    }

    renderReportView(reports[0]);
</script>
</body>
</html>
