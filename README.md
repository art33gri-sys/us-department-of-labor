<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>U.S. Department of Labor | Federal Archive</title>
    <style>
        :root { --primary: #002e6d; --accent: #cd2026; --gold: #b69156; --bg: #f4f4f4; }
        body { font-family: 'Times New Roman', serif; margin: 0; background: var(--bg); color: #333; }
        
        header { background: var(--primary); color: white; padding: 20px 0; border-bottom: 4px solid var(--accent); text-align: center; }
        .container { max-width: 1200px; margin: 0 auto; padding: 0 20px; }
        
        nav { background: #112e51; padding: 10px 0; display: flex; justify-content: center; gap: 15px; position: sticky; top: 0; z-index: 100; }
        .nav-btn { text-decoration: none; color: white; font-weight: bold; padding: 8px 15px; border: 2px solid var(--gold); border-radius: 4px; font-family: sans-serif; cursor: pointer; font-size: 12px; }
        .nav-btn:hover { background: var(--gold); color: black; }

        .paper { background: white; padding: 40px; margin: 30px auto; box-shadow: 0 0 20px rgba(0,0,0,0.2); border: 1px solid #ccc; min-height: 500px; }
        h2 { text-align: center; color: var(--primary); border-bottom: 2px solid var(--primary); margin-bottom: 20px; text-transform: uppercase; }
        h3 { background: #eee; padding: 8px; border-left: 5px solid var(--primary); color: var(--primary); margin-top: 20px; font-size: 16px; }

        /* СЕТКА ОТЧЕТА (3 колонки) */
        .report-grid { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 20px; margin-top: 20px; }
        
        table { width: 100%; border-collapse: collapse; margin: 10px 0; font-size: 14px; }
        table th, table td { padding: 8px; border: 1px solid #ddd; text-align: left; }
        .pct { background: var(--primary); color: white; padding: 2px 5px; border-radius: 3px; font-size: 11px; }

        .editor-box { background: white; border: 2px solid var(--gold); padding: 30px; margin-top: 20px; border-radius: 8px; font-family: sans-serif; }
        .f-grid { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 15px; }
        input, textarea { width: 100%; padding: 10px; margin: 5px 0 10px; border: 1px solid #ccc; border-radius: 4px; box-sizing: border-box; }
        .btn-action { background: #28a745; color: white; border: none; padding: 15px; width: 100%; font-weight: bold; cursor: pointer; border-radius: 4px; }

        .tab { display: none; }
        .active { display: block; }
        .log-line { font-family: monospace; font-size: 12px; padding: 5px; border-bottom: 1px solid #eee; color: #555; }
    </style>
</head>
<body>

<header>
    <div class="container">
        <img src="https://upload.wikimedia.org/wikipedia/commons/d/da/Seal_of_the_United_States_Department_of_Labor.svg" width="70" alt="Seal"><br>
        <h1 style="margin:10px 0 0 0; font-size: 22px;">UNITED STATES DEPARTMENT OF LABOR</h1>
    </div>
</header>

<nav>
    <div class="nav-btn" onclick="switchTab('main')">ГЛАВНАЯ</div>
    <div class="nav-btn" onclick="switchTab('archive')">АРХИВ</div>
    <div class="nav-btn" id="n-edit" style="display:none;" onclick="switchTab('editor')">СОЗДАТЬ ОТЧЕТ</div>
    <div class="nav-btn" id="n-users" style="display:none;" onclick="switchTab('users')">ПЕРСОНАЛ</div>
    <div class="nav-btn" id="n-logs" style="display:none;" onclick="switchTab('logs')">ЛОГИ</div>
    <div class="nav-btn" style="border-color:#5cb85c" onclick="openLogin()">ВХОД</div>
</nav>

<div class="container">
    <div id="tab-main" class="tab active">
        <div class="paper" id="view-report"></div>
    </div>

    <div id="tab-archive" class="tab">
        <h2 style="margin-top:30px;">Архив отчетов</h2>
        <div id="archive-list"></div>
    </div>

    <div id="tab-editor" class="tab">
        <div class="editor-box">
            <h2>Новый еженедельный отчет</h2>
            <label>Период:</label><input type="text" id="in-p" value="05.04.2026 — 12.04.2026">
            
            <div class="f-grid">
                <div>
                    <h3>Аппарат</h3>
                    <label>Лицензии:</label><input type="number" id="in-lic" value="0">
                    <label>Собеседования:</label><input type="number" id="in-int" value="0">
                    <label>Экзамены:</label><input type="number" id="in-ex" value="0">
                </div>
                <div>
                    <h3>Здравоохранение</h3>
                    <label>Надзор:</label><input type="number" id="in-nadz" value="0">
                    <label>Штрафы ($):</label><input type="text" id="in-sh" value="0">
                    <label>Акции:</label><input type="number" id="in-ak" value="0">
                </div>
                <div>
                    <h3>Общая информация</h3>
                    <label>Список сотрудников (Имя|Должность):</label>
                    <textarea id="in-list" rows="5" placeholder="John Doe|Зам.Главы"></textarea>
                    <label>Ссылки на отчеты:</label>
                    <textarea id="in-link" rows="2" placeholder="Отчет|https://..."></textarea>
                </div>
            </div>
            <button class="btn-action" onclick="postReport()">ОПУБЛИКОВАТЬ</button>
        </div>
    </div>

    <div id="tab-users" class="tab">
        <div class="editor-box">
            <h3>Добавить сотрудника</h3>
            <input type="text" id="u-n" placeholder="Имя Фамилия">
            <input type="email" id="u-e" placeholder="Email">
            <input type="password" id="u-p" placeholder="Пароль">
            <button class="btn-action" style="background:var(--primary)" onclick="saveUser()">СОХРАНИТЬ</button>
            <div id="user-display" style="margin-top:20px;"></div>
        </div>
    </div>

    <div id="tab-logs" class="tab">
        <div class="editor-box">
            <h3>Системные логи</h3>
            <div id="log-display" style="max-height:400px; overflow-y:auto; background:#f9f9f9; padding:10px;"></div>
        </div>
    </div>
</div>

<div id="login-modal" style="display:none; position:fixed; top:50%; left:50%; transform:translate(-50%,-50%); background:white; padding:40px; border-top:10px solid var(--primary); z-index:1001; box-shadow:0 0 100px rgba(0,0,0,0.5);">
    <h3>Авторизация</h3>
    <input type="email" id="l-e" placeholder="Email">
    <input type="password" id="l-p" placeholder="Пароль">
    <button class="btn-action" onclick="doLogin()">ВОЙТИ</button>
</div>

<script>
    let db = JSON.parse(localStorage.getItem('gov_final_v6')) || {
        users: [{email: 'minister@dol.gov', pass: 'Laba2026', name: 'Artur Drovic', role: 'Admin'}],
        reports: [{
            id: 1, meta: "Экземпляр №1", period: "05.04.2026 — 12.04.2026",
            data: {lic: 315, int: 6, ex: 40, nadz: 22, sh: "280.000", ak: 3},
            list: "Mops Sergeyevich|Глава\nJamess Soprano|Зам.Главы",
            links: "Основной отчет|#"
        }],
        logs: []
    };

    let user = null;

    function save() { localStorage.setItem('gov_final_v6', JSON.stringify(db)); }

    function addLog(msg) {
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
        let staffRows = r.list.split('\n').map(line => {
            let [n, d] = line.split('|');
            return `<tr><td>${n || '-'}</td><td>${d || '-'}</td></tr>`;
        }).join('');

        let linksHtml = r.links.split('\n').filter(l => l.includes('|')).map(l => {
            let [n, u] = l.split('|'); return `<a href="${u}" target="_blank" style="color:blue; display:block; font-size:12px;">📄 ${n}</a>`;
        }).join('');

        document.getElementById('view-report').innerHTML = `
            <div style="text-align:right; font-style:italic; color:gray; font-size:12px;">${r.meta}</div>
            <p style="margin:0;">Кому: <b>Губернатору Anna Moroz</b></p>
            <p style="margin:0;">От: <b>Министра Artur Drovic</b></p>
            <h2>Еженедельный отчет Министерства Труда</h2>
            <p style="text-align:center;">Период: <b>${r.period}</b></p>
            
            <div class="report-grid">
                <div>
                    <h3>Статистика Аппарата</h3>
                    <table>
                        <tr><td>Выдано лицензий</td><td><b>${r.data.lic}</b></td></tr>
                        <tr><td>Собеседования</td><td>${r.data.int}</td></tr>
                        <tr><td>Экзамены</td><td>${r.data.ex}</td></tr>
                    </table>
                    <h3>Премирование (Министерство)</h3>
                    <table>
                        <tr><td>Artur Drovic</td><td><span class="pct">34%</span></td></tr>
                        <tr><td>Miy Li</td><td><span class="pct">33%</span></td></tr>
                    </table>
                </div>
                <div>
                    <h3>Здравоохранение</h3>
                    <table>
                        <tr><td>Надзор</td><td>${r.data.nadz}</td></tr>
                        <tr><td>Сумма штрафов</td><td><b>${r.data.sh}$</b></td></tr>
                        <tr><td>Акции</td><td>${r.data.ak}</td></tr>
                    </table>
                    <div style="margin-top:20px;">
                        <h3>Документация</h3>
                        ${linksHtml || 'Нет ссылок'}
                    </div>
                </div>
                <div>
                    <h3>Общая информация</h3>
                    <table>
                        <tr><th>Сотрудник</th><th>Должность</th></tr>
                        ${staffRows}
                    </table>
                </div>
            </div>
        `;
    }

    function postReport() {
        let newR = {
            id: Date.now(),
            meta: `Экземпляр №${db.reports.length + 1}`,
            period: document.getElementById('in-p').value,
            data: {
                lic: document.getElementById('in-lic').value,
                int: document.getElementById('in-int').value,
                ex: document.getElementById('in-ex').value,
                nadz: document.getElementById('in-nadz').value,
                sh: document.getElementById('in-sh').value,
                ak: document.getElementById('in-ak').value
            },
            list: document.getElementById('in-list').value,
            links: document.getElementById('in-link').value
        };
        db.reports.unshift(newR);
        addLog(`Опубликован отчет за ${newR.period}`);
        switchTab('main');
        renderReport(db.reports[0]);
    }

    function renderArchive() {
        document.getElementById('archive-list').innerHTML = db.reports.map((r, i) => `
            <div style="background:white; padding:15px; border:1px solid #ddd; margin-bottom:10px; cursor:pointer;" onclick="renderReport(db.reports[${i}]); switchTab('main')">
                <b>${r.period}</b> (${r.meta})
            </div>
        `).join('');
    }

    function renderUsers() {
        document.getElementById('user-display').innerHTML = db.users.map(u => `<div>${u.name} (${u.role})</div>`).join('');
    }

    function renderLogs() {
        document.getElementById('log-display').innerHTML = db.logs.map(l => `<div class="log-line">${l}</div>`).join('');
    }

    function openLogin() { document.getElementById('login-modal').style.display='block'; }
    function doLogin() {
        let u = db.users.find(x => x.email === document.getElementById('l-e').value && x.pass === document.getElementById('l-p').value);
        if(u) {
            user = u;
            document.getElementById('n-edit').style.display='block';
            document.getElementById('n-logs').style.display='block';
            if(u.role==='Admin') document.getElementById('n-users').style.display='block';
            addLog("Успешный вход в систему");
            document.getElementById('login-modal').style.display='none';
        } else { alert("Ошибка!"); }
    }

    function saveUser() {
        let u = {name: document.getElementById('u-n').value, email: document.getElementById('u-e').value, pass: document.getElementById('u-p').value, role: 'Editor'};
        db.users.push(u);
        addLog(`Добавлен новый сотрудник: ${u.name}`);
        renderUsers();
    }

    renderReport(db.reports[0]);
</script>
</body>
</html>
