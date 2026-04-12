<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>U.S. Department of Labor | Federal Management System</title>
    <style>
        :root { --primary: #002e6d; --accent: #cd2026; --gold: #b69156; --bg: #f0f2f5; }
        body { font-family: 'Times New Roman', serif; margin: 0; background: var(--bg); color: #333; }
        
        header { background: var(--primary); color: white; padding: 15px 0; border-bottom: 4px solid var(--accent); }
        .container { max-width: 1000px; margin: 0 auto; padding: 0 20px; }
        .header-flex { display: flex; align-items: center; justify-content: space-between; }
        .logo-box { display: flex; align-items: center; gap: 15px; }
        .logo-box img { width: 70px; height: 70px; }

        nav { background: #112e51; padding: 10px 0; display: flex; justify-content: center; gap: 10px; position: sticky; top: 0; z-index: 100; }
        .nav-btn { text-decoration: none; color: white; font-weight: bold; padding: 8px 18px; border: 2px solid var(--gold); border-radius: 4px; font-family: sans-serif; cursor: pointer; font-size: 13px; }
        .nav-btn:hover { background: var(--gold); color: black; }

        .paper { background: white; padding: 50px; margin: 25px auto; box-shadow: 0 0 20px rgba(0,0,0,0.1); border: 1px solid #ccc; position: relative; }
        h2 { text-align: center; color: var(--primary); border-bottom: 2px solid var(--primary); text-transform: uppercase; }
        h3 { background: #f1f1f1; padding: 8px; border-left: 5px solid var(--primary); color: var(--primary); margin-top: 25px; }

        table { width: 100%; border-collapse: collapse; margin: 10px 0; font-size: 15px; }
        table th, table td { padding: 10px; border: 1px solid #ddd; text-align: left; }
        .pct { background: var(--primary); color: white; padding: 2px 6px; border-radius: 3px; font-family: sans-serif; }

        .admin-block { display: none; background: white; border: 2px solid var(--gold); padding: 25px; margin-top: 20px; border-radius: 8px; font-family: sans-serif; }
        .form-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; }
        input, select, textarea { width: 100%; padding: 10px; margin: 5px 0; border: 1px solid #ccc; border-radius: 4px; box-sizing: border-box; }
        
        .btn-post { background: #28a745; color: white; border: none; padding: 15px; cursor: pointer; width: 100%; font-weight: bold; font-size: 16px; margin-top: 20px; }
        .btn-del { background: var(--accent); color: white; border: none; padding: 5px 10px; cursor: pointer; font-size: 12px; border-radius: 3px; }

        .log-entry { font-family: monospace; font-size: 12px; padding: 5px; border-bottom: 1px solid #eee; }
        .tab { display: none; }
        .active { display: block; }
    </style>
</head>
<body>

<header>
    <div class="container header-flex">
        <div class="logo-box">
            <img src="https://upload.wikimedia.org/wikipedia/commons/d/da/Seal_of_the_United_States_Department_of_Labor.svg" alt="Seal">
            <div><h1 style="margin:0; font-size: 22px;">U.S. DEPARTMENT OF LABOR</h1><p style="margin:0; font-size:12px; opacity:0.8;">FEDERAL MANAGEMENT & ARCHIVE</p></div>
        </div>
        <div id="user-info" style="font-family:sans-serif; font-size:13px; color: var(--gold);"></div>
    </div>
</header>

<nav>
    <div class="nav-btn" onclick="switchTab('main')">ГЛАВНАЯ</div>
    <div class="nav-btn" onclick="switchTab('archive')">АРХИВ</div>
    <div class="nav-btn" id="m-edit" style="display:none;" onclick="switchTab('editor')">НОВЫЙ ОТЧЕТ</div>
    <div class="nav-btn" id="m-users" style="display:none;" onclick="switchTab('users')">ПЕРСОНАЛ</div>
    <div class="nav-btn" id="m-logs" style="display:none;" onclick="switchTab('logs')">ЛОГИ</div>
    <div class="nav-btn" style="border-color:#5cb85c" onclick="openLogin()">ВХОД</div>
</nav>

<div class="container">

    <div id="tab-main" class="tab active">
        <div class="paper" id="current-view"></div>
    </div>

    <div id="tab-archive" class="tab">
        <h2 style="margin-top:30px;">Архив Государственных Отчетов</h2>
        <div id="archive-list"></div>
    </div>

    <div id="tab-editor" class="tab">
        <div class="admin-block">
            <h3>Создание нового еженедельного отчета</h3>
            <div class="form-grid">
                <div>
                    <label>Период отчета:</label><input type="text" id="f-period" value="05.04.2026 — 12.05.2026">
                    <h4>Аппарат Правительства</h4>
                    <label>Лицензии (всего):</label><input type="number" id="f-lic" value="315">
                    <label>Оружие:</label><input type="number" id="f-gun" value="239">
                    <label>Собеседования:</label><input type="number" id="f-int" value="6">
                    <label>Стажировки:</label><input type="number" id="f-staj" value="47">
                </div>
                <div>
                    <h4>Здравоохранение</h4>
                    <label>Штрафы ($):</label><input type="text" id="f-sh-val" value="280.000">
                    <label>Акции ($):</label><input type="text" id="f-ak-val" value="101.000">
                    <label>Надзор:</label><input type="number" id="f-nadz" value="22">
                    <label>Мед. проверки:</label><input type="number" id="f-med" value="4">
                </div>
            </div>
            <h4>Документы</h4>
            <textarea id="f-docs" rows="2">Отчет Аппарата|#&#10;Отчет Здравоохранения|#</textarea>
            <button class="btn-post" onclick="publishReport()">ОПУБЛИКОВАТЬ И В АРХИВ</button>
        </div>
    </div>

    <div id="tab-users" class="tab">
        <div class="admin-block">
            <h3>Управление сотрудниками</h3>
            <div id="user-table-container"></div>
            <hr>
            <h4>Добавить нового сотрудника</h4>
            <input type="text" id="u-name" placeholder="Имя Фамилия">
            <input type="email" id="u-email" placeholder="Email">
            <input type="password" id="u-pass" placeholder="Пароль">
            <select id="u-role"><option value="Editor">Заместитель (Editor)</option><option value="Admin">Министр (Admin)</option></select>
            <button class="btn-post" style="background:var(--primary)" onclick="addUser()">ЗАРЕГИСТРИРОВАТЬ</button>
        </div>
    </div>

    <div id="tab-logs" class="tab">
        <div class="admin-block">
            <h3>История системных действий</h3>
            <div id="logs-list" style="max-height:400px; overflow-y:auto; background:#f9f9f9; padding:10px;"></div>
        </div>
    </div>

</div>

<div id="l-mod" style="display:none; position:fixed; top:50%; left:50%; transform:translate(-50%,-50%); background:white; padding:30px; border-top:10px solid var(--primary); z-index:1001; box-shadow:0 0 100px rgba(0,0,0,0.5);">
    <h3 style="margin:0 0 15px 0;">System Access</h3>
    <input type="email" id="l-e" placeholder="Email">
    <input type="password" id="l-p" placeholder="Password">
    <button class="btn-post" onclick="doLogin()">ВОЙТИ</button>
    <center><button onclick="closeLogin()" style="background:none; border:none; color:gray; cursor:pointer; margin-top:10px;">Отмена</button></center>
</div>

<script>
    // --- DATABASE ---
    let db = JSON.parse(localStorage.getItem('gov_v3_db')) || {
        users: [
            {email: 'minister@dol.gov', pass: 'Laba2026', name: 'Artur Drovic', role: 'Admin'},
            {email: 'deputy@dol.gov', pass: 'Gov2026', name: 'Miy Li', role: 'Editor'}
        ],
        reports: [{
            id: Date.now(),
            meta: "Экземпляр №1 | 12.05.2026",
            period: "05.04.2026 — 12.05.2026",
            data: { lic: 315, gun: 239, int: 6, staj: 47, sh: "280.000", ak: "101.000", nadz: 22, med: 4 },
            docs: "Отчет Аппарата|#\nОтчет Здравоохранения|#"
        }],
        logs: []
    };

    let user = null;

    function save() { localStorage.setItem('gov_v3_db', JSON.stringify(db)); }

    function log(msg) {
        db.logs.unshift(`[${new Date().toLocaleString()}] ${user ? user.name : 'System'}: ${msg}`);
        save();
    }

    function switchTab(t) {
        document.querySelectorAll('.tab').forEach(x => x.classList.remove('active'));
        document.getElementById('tab-' + t).classList.add('active');
        if(t === 'archive') renderArchive();
        if(t === 'users') renderUsers();
        if(t === 'logs') renderLogs();
    }

    function renderReport(r) {
        let docs = r.docs.split('\n').map(l => {
            let [n, u] = l.split('|'); return `<a href="${u}" style="color:blue; display:block; margin:5px 0;">📄 ${n}</a>`;
        }).join('');

        document.getElementById('current-view').innerHTML = `
            <div style="font-style:italic; color:gray; margin-bottom:15px;">${r.meta}</div>
            <h2>Еженедельный отчет Министерства Труда</h2>
            <p style="text-align:center; font-size:18px;">Период: <b>${r.period}</b></p>
            
            <h3>1. Министерство (Аудит Премий)</h3>
            <table><tr><th>Сотрудник</th><th>Доля</th></tr>
            <tr><td>Artur Drovic (Министр)</td><td><span class="pct">34%</span></td></tr>
            <tr><td>Miy Li (Зам)</td><td><span class="pct">33%</span></td></tr>
            <tr><td>Elmer Moroz (Зам)</td><td><span class="pct">33%</span></td></tr></table>

            <h3>2. Аппарат Правительства</h3>
            <table>
                <tr><td>Выдано лицензий</td><td><b>${r.data.lic}</b> (Оружие: ${r.data.gun})</td></tr>
                <tr><td>Собеседования / Стажировки</td><td>${r.data.int} / ${r.data.staj}</td></tr>
            </table>

            <h3>3. Департамент Здравоохранения</h3>
            <table>
                <tr><td>Штрафы / Акции</td><td><b>${r.data.sh}$ / ${r.data.ak}$</b></td></tr>
                <tr><td>Надзор / Мед. проверки</td><td>${r.data.nadz} / ${r.data.med}</td></tr>
            </table>

            <h3>Прикрепленные документы</h3>
            <div style="background:#f9f9f9; padding:15px; border:1px dashed #ccc;">${docs}</div>
        `;
    }

    function publishReport() {
        let newR = {
            id: Date.now(),
            meta: `Экземпляр №${db.reports.length + 1} | ${new Date().toLocaleDateString()}`,
            period: document.getElementById('f-period').value,
            data: {
                lic: document.getElementById('f-lic').value,
                gun: document.getElementById('f-gun').value,
                int: document.getElementById('f-int').value,
                staj: document.getElementById('f-staj').value,
                sh: document.getElementById('f-sh-val').value,
                ak: document.getElementById('f-ak-val').value,
                nadz: document.getElementById('f-nadz').value,
                med: document.getElementById('f-med').value
            },
            docs: document.getElementById('f-docs').value
        };
        db.reports.unshift(newR);
        log(`Создан новый отчет (${newR.period})`);
        save();
        location.reload();
    }

    function deleteReport(id) {
        if(confirm("Удалить отчет из архива навсегда?")) {
            db.reports = db.reports.filter(x => x.id !== id);
            log(`Удален отчет ID: ${id}`);
            save();
            renderArchive();
        }
    }

    function renderArchive() {
        document.getElementById('archive-list').innerHTML = db.reports.map((r, i) => `
            <div style="background:white; padding:15px; margin-bottom:10px; border:1px solid #ddd; display:flex; justify-content:space-between; align-items:center;">
                <div onclick="renderReport(db.reports[${i}]); switchTab('main');" style="cursor:pointer;">
                    <b>${r.period}</b><br><small>${r.meta}</small>
                </div>
                ${user && user.role === 'Admin' ? `<button class="btn-del" onclick="deleteReport(${r.id})">Удалить</button>` : ''}
            </div>
        `).join('');
    }

    function addUser() {
        let u = { email: document.getElementById('u-email').value, pass: document.getElementById('u-pass').value, name: document.getElementById('u-name').value, role: document.getElementById('u-role').value };
        db.users.push(u);
        log(`Добавлен пользователь: ${u.name}`);
        save();
        renderUsers();
    }

    function delUser(idx) {
        if(db.users[idx].email === 'minister@dol.gov') return alert("Нельзя удалить главного Министра!");
        log(`Удален пользователь: ${db.users[idx].name}`);
        db.users.splice(idx, 1);
        save();
        renderUsers();
    }

    function renderUsers() {
        let html = '<table><tr><th>Имя</th><th>Email</th><th>Роль</th><th>Действие</th></tr>';
        db.users.forEach((u, i) => {
            html += `<tr><td>${u.name}</td><td>${u.email}</td><td>${u.role}</td><td><button class="btn-del" onclick="delUser(${i})">Удалить</button></td></tr>`;
        });
        document.getElementById('user-table-container').innerHTML = html + '</table>';
    }

    function renderLogs() { document.getElementById('logs-list').innerHTML = db.logs.map(x => `<div class="log-entry">${x}</div>`).join(''); }

    function openLogin() { document.getElementById('l-mod').style.display='block'; }
    function closeLogin() { document.getElementById('l-mod').style.display='none'; }

    function doLogin() {
        let e = document.getElementById('l-e').value;
        let p = document.getElementById('l-p').value;
        let u = db.users.find(x => x.email === e && x.pass === p);
        if(u) {
            user = u;
            document.getElementById('user-info').innerHTML = `Active: <b>${u.name}</b>`;
            document.getElementById('m-edit').style.display = 'block';
            if(u.role === 'Admin') {
                document.getElementById('m-users').style.display = 'block';
                document.getElementById('m-logs').style.display = 'block';
            }
            log("Вход в систему");
            closeLogin();
        } else { alert("Отказ в доступе"); }
    }

    // Start
    renderReport(db.reports[0]);
</script>
</body>
</html>
