<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>U.S. Department of Labor | Management System</title>
    <style>
        :root { --primary: #002e6d; --accent: #cd2026; --gold: #b69156; --bg: #f4f4f4; }
        body { font-family: 'Times New Roman', serif; margin: 0; background: var(--bg); color: #333; }
        
        header { background: var(--primary); color: white; padding: 20px 0; border-bottom: 4px solid var(--accent); text-align: center; }
        .container { max-width: 1000px; margin: 0 auto; padding: 0 20px; }
        
        nav { background: #112e51; padding: 10px 0; display: flex; justify-content: center; gap: 15px; position: sticky; top: 0; z-index: 100; }
        .nav-btn { text-decoration: none; color: white; font-weight: bold; padding: 8px 15px; border: 2px solid var(--gold); border-radius: 4px; font-family: sans-serif; cursor: pointer; font-size: 12px; }
        .nav-btn:hover { background: var(--gold); color: black; }

        .paper { background: white; padding: 60px; margin: 30px auto; box-shadow: 0 0 20px rgba(0,0,0,0.2); border: 1px solid #ccc; min-height: 400px; }
        h2 { text-align: center; color: var(--primary); border-bottom: 2px solid var(--primary); margin-bottom: 30px; }
        h3 { background: #eee; padding: 10px; border-left: 6px solid var(--primary); color: var(--primary); margin-top: 30px; text-transform: uppercase; font-size: 18px; }

        table { width: 100%; border-collapse: collapse; margin: 15px 0; }
        table th, table td { padding: 12px; border: 1px solid #ddd; text-align: left; }
        .pct { background: var(--primary); color: white; padding: 2px 6px; border-radius: 3px; font-family: sans-serif; font-size: 12px; }

        /* ИСПРАВЛЕННЫЙ БЛОК РЕДАКТОРА */
        .editor-container { background: white; border: 2px solid var(--gold); padding: 30px; margin-top: 20px; border-radius: 8px; font-family: sans-serif; }
        .form-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; }
        input, textarea { width: 100%; padding: 12px; margin: 8px 0 18px; border: 1px solid #ccc; border-radius: 4px; box-sizing: border-box; font-size: 14px; }
        .btn-post { background: #28a745; color: white; border: none; padding: 18px; width: 100%; font-weight: bold; cursor: pointer; font-size: 16px; border-radius: 4px; text-transform: uppercase; }
        .btn-post:hover { background: #218838; }

        .tab { display: none; }
        .active { display: block; }
        .archive-item { background: white; padding: 15px; margin-bottom: 10px; border: 1px solid #ddd; cursor: pointer; display: flex; justify-content: space-between; align-items: center; }
    </style>
</head>
<body>

<header>
    <div class="container">
        <img src="https://upload.wikimedia.org/wikipedia/commons/d/da/Seal_of_the_United_States_Department_of_Labor.svg" width="80" alt="Seal"><br>
        <h1 style="margin:10px 0 0 0; font-size: 24px;">UNITED STATES DEPARTMENT OF LABOR</h1>
    </div>
</header>

<nav>
    <div class="nav-btn" onclick="switchTab('main')">ГЛАВНАЯ</div>
    <div class="nav-btn" onclick="switchTab('archive')">АРХИВ</div>
    <div class="nav-btn" id="n-edit" style="display:none;" onclick="switchTab('editor')">СОЗДАТЬ ОТЧЕТ</div>
    <div class="nav-btn" id="n-users" style="display:none;" onclick="switchTab('users')">ПЕРСОНАЛ</div>
    <div class="nav-btn" style="border-color:#5cb85c" onclick="openLogin()">ВХОД</div>
</nav>

<div class="container">
    <div id="tab-main" class="tab active">
        <div class="paper" id="current-report-content">
            </div>
    </div>

    <div id="tab-archive" class="tab">
        <h2 style="margin-top:40px;">Архив департамента</h2>
        <div id="archive-list"></div>
    </div>

    <div id="tab-editor" class="tab">
        <div class="editor-container">
            <h2>Создание нового еженедельного отчета</h2>
            <label>Период отчета (напр. 05.04.2026 — 12.04.2026):</label>
            <input type="text" id="in-period" placeholder="Введите даты...">

            <div class="form-grid">
                <div>
                    <h3>Аппарат Правительства</h3>
                    <label>Лицензий (всего):</label><input type="number" id="in-lic" value="0">
                    <label>Оружие:</label><input type="number" id="in-gun" value="0">
                    <label>Собеседования:</label><input type="number" id="in-int" value="0">
                    <label>Стажировки:</label><input type="number" id="in-staj" value="0">
                </div>
                <div>
                    <h3>Здравоохранение</h3>
                    <label>Надзорные дела:</label><input type="number" id="in-nadz" value="0">
                    <label>Мед. проверки:</label><input type="number" id="in-med" value="0">
                    <label>Штрафы ($):</label><input type="text" id="in-sh" value="0">
                    <label>Акции:</label><input type="number" id="in-ak" value="0">
                </div>
            </div>

            <label>Ссылки на доп. документы (Название|Ссылка, каждая с новой строки):</label>
            <textarea id="in-links" rows="4" placeholder="Отчет Главы|https://..."></textarea>

            <button class="btn-post" onclick="createNewReport()">Опубликовать в систему</button>
        </div>
    </div>

    <div id="tab-users" class="tab">
        <div class="editor-container">
            <h3>Добавить сотрудника</h3>
            <input type="text" id="u-name" placeholder="Имя Фамилия">
            <input type="email" id="u-email" placeholder="Email">
            <input type="password" id="u-pass" placeholder="Пароль">
            <button class="btn-post" style="background:var(--primary)" onclick="saveUser()">Зарегистрировать</button>
            <div id="user-list-display" style="margin-top:20px;"></div>
        </div>
    </div>
</div>

<div id="login-modal" style="display:none; position:fixed; top:50%; left:50%; transform:translate(-50%,-50%); background:white; padding:40px; border-top:10px solid var(--primary); z-index:1001; box-shadow:0 0 100px rgba(0,0,0,0.5); border-radius: 8px;">
    <h3 style="margin-top:0;">Access Portal</h3>
    <input type="email" id="l-email" placeholder="Email">
    <input type="password" id="l-pass" placeholder="Пароль">
    <button class="btn-post" onclick="doLogin()">Войти</button>
    <center><button onclick="closeLogin()" style="background:none; border:none; color:gray; cursor:pointer; margin-top:15px;">Отмена</button></center>
</div>

<script>
    // БАЗА ДАННЫХ В ПАМЯТИ
    let data = JSON.parse(localStorage.getItem('dol_system_v4')) || {
        users: [{email: 'minister@dol.gov', pass: 'Laba2026', name: 'Artur Drovic', role: 'Admin'}],
        reports: [{
            id: 1,
            meta: "Экземпляр №1 | 12.04.2026",
            period: "05.04.2026 — 12.04.2026",
            content: {lic: 315, gun: 239, int: 6, staj: 47, nadz: 22, med: 4, sh: "280.000", ak: 3},
            links: "Отчет Аппарата|#\nОтчет Здравоохранения|#"
        }]
    };

    let currentUser = null;

    function saveDB() { localStorage.setItem('dol_system_v4', JSON.stringify(data)); }

    function switchTab(t) {
        document.querySelectorAll('.tab').forEach(x => x.classList.remove('active'));
        document.getElementById('tab-' + t).classList.add('active');
        if(t === 'archive') renderArchive();
        if(t === 'users') renderUserList();
    }

    function renderReport(report) {
        let links = report.links.split('\n').filter(l => l.includes('|')).map(l => {
            let [name, url] = l.split('|');
            return `<a href="${url}" target="_blank" style="color:#0056b3; display:block; margin:5px 0; font-weight:bold;">📄 ${name}</a>`;
        }).join('');

        document.getElementById('current-report-content').innerHTML = `
            <div style="text-align:right; font-style:italic; color:gray;">${report.meta}</div>
            <p>Кому: <b>Губернатору Anna Moroz</b><br>От: <b>Министра Artur Drovic</b></p>
            <h2>Еженедельный отчет Министерства Труда</h2>
            <p style="text-align:center;">Отчетный период: <b>${report.period}</b></p>
            
            <h3>1. Министерство (Аудит)</h3>
            <table>
                <tr><td>Artur Drovic (Министр)</td><td><span class="pct">34%</span></td></tr>
                <tr><td>Miy Li (Зам)</td><td><span class="pct">33%</span></td></tr>
                <tr><td>Elmer Moroz (Зам)</td><td><span class="pct">33%</span></td></tr>
            </table>

            <h3>2. Аппарат Правительства</h3>
            <table>
                <tr><td>Лицензий выдано</td><td><b>${report.content.lic}</b></td></tr>
                <tr><td>Из них на оружие</td><td>${report.content.gun}</td></tr>
                <tr><td>Собеседования / Стажировки</td><td>${report.content.int} / ${report.content.staj}</td></tr>
            </table>

            <h3>3. Департамент Здравоохранения</h3>
            <table>
                <tr><td>Надзорные мероприятия</td><td>${report.content.nadz}</td></tr>
                <tr><td>Мед. проверки</td><td>${report.content.med}</td></tr>
                <tr><td>Сумма штрафов</td><td><b>${report.content.sh}$</b></td></tr>
                <tr><td>Проведенные акции</td><td>${report.content.ak}</td></tr>
            </table>

            <h3>Прикрепленные документы</h3>
            <div style="background:#f8f9fa; padding:15px; border:1px dashed #ccc;">${links || 'Документы отсутствуют'}</div>
        `;
    }

    function createNewReport() {
        let newR = {
            id: Date.now(),
            meta: `Экземпляр №${data.reports.length + 1} | ${new Date().toLocaleDateString()}`,
            period: document.getElementById('in-period').value || "Не указан",
            content: {
                lic: document.getElementById('in-lic').value,
                gun: document.getElementById('in-gun').value,
                int: document.getElementById('in-int').value,
                staj: document.getElementById('in-staj').value,
                nadz: document.getElementById('in-nadz').value,
                med: document.getElementById('in-med').value,
                sh: document.getElementById('in-sh').value,
                ak: document.getElementById('in-ak').value
            },
            links: document.getElementById('in-links').value
        };
        data.reports.unshift(newR);
        saveDB();
        alert("Опубликовано!");
        switchTab('main');
        renderReport(data.reports[0]);
    }

    function renderArchive() {
        document.getElementById('archive-list').innerHTML = data.reports.map((r, i) => `
            <div class="archive-item">
                <div onclick="renderReport(data.reports[${i}]); switchTab('main')" style="flex-grow:1;">
                    <b>${r.period}</b><br><small>${r.meta}</small>
                </div>
                ${currentUser && currentUser.role === 'Admin' ? `<button onclick="deleteReport(${r.id})" style="color:red; border:none; background:none; cursor:pointer;">Удалить</button>` : ''}
            </div>
        `).join('');
    }

    function deleteReport(id) {
        if(confirm("Удалить отчет?")) {
            data.reports = data.reports.filter(x => x.id !== id);
            saveDB();
            renderArchive();
        }
    }

    function openLogin() { document.getElementById('login-modal').style.display='block'; }
    function closeLogin() { document.getElementById('login-modal').style.display='none'; }

    function doLogin() {
        let e = document.getElementById('l-email').value;
        let p = document.getElementById('l-pass').value;
        let u = data.users.find(x => x.email === e && x.pass === p);
        if(u) {
            currentUser = u;
            document.getElementById('n-edit').style.display = 'block';
            if(u.role === 'Admin') document.getElementById('n-users').style.display = 'block';
            closeLogin();
            alert("Добро пожаловать, " + u.name);
        } else { alert("Ошибка авторизации"); }
    }

    function saveUser() {
        data.users.push({name: document.getElementById('u-name').value, email: document.getElementById('u-email').value, pass: document.getElementById('u-pass').value, role: 'Editor'});
        saveDB();
        renderUserList();
    }

    function renderUserList() {
        document.getElementById('user-list-display').innerHTML = data.users.map(u => `<div>${u.name} (${u.role})</div>`).join('');
    }

    // Запуск
    renderReport(data.reports[0]);
</script>
</body>
</html>
