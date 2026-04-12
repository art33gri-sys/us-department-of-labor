<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>U.S. Department of Labor | System Portal</title>
    <style>
        :root {
            --primary: #002e6d;
            --secondary: #205493;
            --accent: #cd2026;
            --bg: #f4f4f4;
            --white: #ffffff;
        }

        body { font-family: 'Georgia', serif; margin: 0; background: var(--bg); color: #333; }
        
        header { background: var(--primary); color: white; padding: 20px 0; border-bottom: 4px solid var(--accent); }
        .container { max-width: 1000px; margin: 0 auto; padding: 0 20px; }
        
        .header-content { display: flex; align-items: center; gap: 20px; }
        .logo-img { width: 80px; height: 80px; background: white; border-radius: 50%; padding: 5px; }
        
        .meta-info { margin-top: 10px; font-size: 13px; font-style: italic; color: #555; }

        nav { background: var(--secondary); padding: 10px 0; }
        .nav-flex { display: flex; justify-content: space-between; }
        nav a, .admin-link { color: white; text-decoration: none; font-weight: bold; font-family: sans-serif; font-size: 14px; cursor: pointer; }

        .report-paper { background: var(--white); padding: 50px; margin: 30px 0; box-shadow: 0 0 20px rgba(0,0,0,0.1); border: 1px solid #ddd; }
        
        h2 { border-bottom: 2px solid var(--primary); padding-bottom: 10px; color: var(--primary); text-transform: uppercase; text-align: center; }
        h3 { background: #eef2f7; padding: 12px; border-left: 6px solid var(--primary); margin-top: 40px; }

        table { width: 100%; border-collapse: collapse; margin: 15px 0; }
        table td { padding: 12px; border-bottom: 1px solid #eee; }
        .label { font-weight: bold; width: 50%; }
        .badge { background: var(--primary); color: white; padding: 4px 10px; border-radius: 4px; font-family: sans-serif; font-size: 13px; }

        /* Форма редактирования */
        #editor-section { display: none; background: #fff; padding: 30px; border: 2px solid var(--primary); margin: 20px 0; }
        input, textarea { width: 100%; padding: 10px; margin: 5px 0 15px; border: 1px solid #ccc; box-sizing: border-box; }
        .btn-save { background: #27ae60; color: white; border: none; padding: 15px; width: 100%; font-weight: bold; cursor: pointer; }
        
        /* Архив */
        #archive-list { display: none; }
        .archive-item { background: white; padding: 15px; margin-bottom: 10px; border-left: 5px solid var(--secondary); cursor: pointer; }
        .archive-item:hover { background: #f9f9f9; }

        .doc-link { color: #2980b9; text-decoration: underline; font-weight: bold; }
        
        #auth-modal { display: none; position: fixed; top: 50%; left: 50%; transform: translate(-50%, -50%); background: white; padding: 30px; box-shadow: 0 0 50px rgba(0,0,0,0.5); z-index: 100; border-top: 5px solid var(--primary); }
        .overlay { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.7); z-index: 99; }
    </style>
</head>
<body>

<header>
    <div class="container header-content">
        <div class="logo-img">
            <img src="https://i.ibb.co/VWVm0Qz/dol-logo.png" alt="DOL" style="width:100%;" onerror="this.src='https://upload.wikimedia.org/wikipedia/commons/d/da/Seal_of_the_United_States_Department_of_Labor.svg'">
        </div>
        <div>
            <h1 style="margin:0; font-size: 28px;">UNITED STATES DEPARTMENT OF LABOR</h1>
            <p style="margin:0; opacity: 0.8;">OFFICIAL SECRETARY OF LABOR PORTAL</p>
        </div>
    </div>
</header>

<nav>
    <div class="container nav-flex">
        <div>
            <a onclick="showSection('main')">ГЛАВНАЯ</a> | 
            <a onclick="showSection('archive')">АРХИВ</a>
        </div>
        <a class="admin-link" onclick="openLogin()">ВХОД В СИСТЕМУ</a>
    </div>
</nav>

<div class="container">
    <div id="meta-container" class="meta-info"></div>

    <div id="editor-section">
        <h3>СОЗДАТЬ НОВЫЙ ЕЖЕНЕДЕЛЬНЫЙ ОТЧЕТ</h3>
        <label>Дата и Номер экземпляра:</label>
        <input type="text" id="edit-meta" placeholder="Пример: Экземпляр №1 | Дата: 12.05.2026">
        
        <label>Период отчета:</label>
        <input type="text" id="edit-period" placeholder="05.04.2026 — 12.05.2026">

        <label>Кадровый аудит Министра (HTML):</label>
        <textarea id="edit-audit-min" rows="3"></textarea>

        <label>Данные Аппарата (Лицензии, собесы и т.д.):</label>
        <textarea id="edit-app" rows="4"></textarea>

        <label>Данные Здравоохранения (Штрафы, проверки):</label>
        <textarea id="edit-zdrav" rows="4"></textarea>

        <label>Ссылки на документы (Название|Ссылка, каждая с новой строки):</label>
        <textarea id="edit-docs" rows="3" placeholder="Название отчета|https://google.com"></textarea>

        <button class="btn-save" onclick="saveNewReport()">ОПУБЛИКОВАТЬ ОТЧЕТ</button>
    </div>

    <div id="main-section">
        <div class="report-paper" id="current-report">
            </div>
    </div>

    <div id="archive-section" style="display:none; margin-top: 30px;">
        <h2>Архив государственных отчетов</h2>
        <div id="archive-container"></div>
    </div>
</div>

<div class="overlay" onclick="closeLogin()"></div>
<div id="auth-modal">
    <h3>Авторизация</h3>
    <input type="email" id="login-email" placeholder="minister@labor.gov.us">
    <input type="password" id="login-pass" placeholder="Пароль">
    <button class="btn-save" onclick="doLogin()">Войти</button>
</div>

<script>
    // Изначальные данные (База данных в памяти браузера)
    let reports = JSON.parse(localStorage.getItem('gov_reports')) || [{
        id: 1,
        meta: "Экземпляр №1 | Дата формирования: 12.05.2026",
        period: "05.04.2026 — 12.05.2026",
        auditMin: `
            <tr><td class="label">Министр Artur Drovic</td><td><span class="badge">34%</span></td></tr>
            <tr><td class="label">Зам. Министра Miy Li</td><td><span class="badge">33%</span></td></tr>
            <tr><td class="label">Зам. Министра Elmer Moroz</td><td><span class="badge">33%</span></td></tr>
        `,
        appData: `
            <tr><td class="label">Глава Аппарата Mops Sergeyevich</td><td><span class="badge">60%</span></td></tr>
            <tr><td class="label">Зам. Главы Jamess Soprano</td><td><span class="badge">40%</span></td></tr>
            <tr><td class="label">Выдано лицензий</td><td><b>315</b></td></tr>
            <tr><td class="label">Принято на стажировку</td><td>47 человек</td></tr>
        `,
        zdravData: `
            <tr><td class="label">Директор Dia Dema</td><td><span class="badge">50%</span></td></tr>
            <tr><td class="label">Зам. Директора Dis Morales</td><td><span class="badge">40%</span></td></tr>
            <tr><td class="label">Общая сумма штрафов</td><td><b>280.000$</b></td></tr>
        `,
        docs: "Еженедельный отчет Аппарата|https://docs.google.com\nЕженедельный отчет Здравоохранения|https://docs.google.com"
    }];

    let isAdmin = false;

    function renderReport(report) {
        document.getElementById('meta-container').innerText = report.meta;
        
        let docsHtml = report.docs.split('\n').map(line => {
            let [name, url] = line.split('|');
            return `<li><a href="${url}" class="doc-link" target="_blank">${name}</a></li>`;
        }).join('');

        document.getElementById('current-report').innerHTML = `
            <h2>Еженедельный отчет Министерства Труда</h2>
            <p style="text-align:center;">Период: <b>${report.period}</b></p>
            
            <h3>1. Кадровый состав и Аудит (Министерство)</h3>
            <table>${report.auditMin}</table>

            <h3>2. Деятельность Аппарата Правительства</h3>
            <table>${report.appData}</table>

            <h3>3. Департамент Здравоохранения</h3>
            <table>${report.zdravData}</table>

            <h3 style="border-left-color: var(--accent);">Прикрепленные документы</h3>
            <ul>${docsHtml}</ul>
        `;
    }

    function showSection(name) {
        document.getElementById('main-section').style.display = name === 'main' ? 'block' : 'none';
        document.getElementById('archive-section').style.display = name === 'archive' ? 'block' : 'none';
        if(name === 'archive') renderArchive();
    }

    function renderArchive() {
        const container = document.getElementById('archive-container');
        container.innerHTML = '';
        reports.forEach((r, index) => {
            const item = document.createElement('div');
            item.className = 'archive-item';
            item.innerHTML = `<strong>Отчет за период: ${r.period}</strong><br><small>${r.meta}</small>`;
            item.onclick = () => { renderReport(r); showSection('main'); };
            container.appendChild(item);
        });
    }

    function openLogin() {
        document.querySelector('.overlay').style.display = 'block';
        document.getElementById('auth-modal').style.display = 'block';
    }

    function closeLogin() {
        document.querySelector('.overlay').style.display = 'none';
        document.getElementById('auth-modal').style.display = 'none';
    }

    function doLogin() {
        const email = document.getElementById('login-email').value;
        const pass = document.getElementById('login-pass').value;
        if(email === 'minister@labor.gov.us' && pass === '1234') {
            isAdmin = true;
            document.getElementById('editor-section').style.display = 'block';
            closeLogin();
            alert('Доступ разрешен, господин Министр!');
        } else {
            alert('Ошибка доступа!');
        }
    }

    function saveNewReport() {
        const newReport = {
            id: Date.now(),
            meta: document.getElementById('edit-meta').value,
            period: document.getElementById('edit-period').value,
            auditMin: document.getElementById('edit-audit-min').value,
            appData: document.getElementById('edit-app').value,
            zdravData: document.getElementById('edit-zdrav').value,
            docs: document.getElementById('edit-docs').value
        };
        reports.unshift(newReport); // Новый отчет в начало
        localStorage.setItem('gov_reports', JSON.stringify(reports));
        renderReport(newReport);
        alert('Отчет опубликован и сохранен в архив!');
        location.reload(); // Перегрузим для чистоты
    }

    // Инициализация при загрузке
    renderReport(reports[0]);
</script>

</body>
</html>
