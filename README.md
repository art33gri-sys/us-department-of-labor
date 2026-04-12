<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>U.S. Department of Labor | Federal Management System</title>
    <style>
        :root { --primary: #002e6d; --accent: #cd2026; --gold: #b69156; --bg: #f4f4f4; }
        body { font-family: 'Times New Roman', serif; margin: 0; background: var(--bg); color: #333; }
        
        header { background: var(--primary); color: white; padding: 20px 0; border-bottom: 4px solid var(--accent); text-align: center; }
        .container { max-width: 1100px; margin: 0 auto; padding: 0 20px; }
        
        nav { background: #112e51; padding: 10px 0; display: flex; justify-content: center; gap: 15px; position: sticky; top: 0; z-index: 100; }
        .nav-btn { text-decoration: none; color: white; font-weight: bold; padding: 8px 15px; border: 2px solid var(--gold); border-radius: 4px; font-family: sans-serif; cursor: pointer; font-size: 12px; }
        .nav-btn:hover { background: var(--gold); color: black; }

        .paper { background: white; padding: 60px; margin: 30px auto; box-shadow: 0 0 20px rgba(0,0,0,0.2); border: 1px solid #ccc; min-height: 500px; }
        h2 { text-align: center; color: var(--primary); border-bottom: 2px solid var(--primary); margin-bottom: 30px; text-transform: uppercase; }
        h3 { background: #eee; padding: 10px; border-left: 6px solid var(--primary); color: var(--primary); margin-top: 30px; text-transform: uppercase; font-size: 16px; }

        .row-split { display: grid; grid-template-columns: 1.2fr 0.8fr; gap: 40px; margin-bottom: 20px; }
        
        table { width: 100%; border-collapse: collapse; margin: 10px 0; }
        table th, table td { padding: 10px; border: 1px solid #ddd; text-align: left; font-size: 14px; }
        .pct { background: var(--primary); color: white; padding: 2px 6px; border-radius: 3px; font-family: sans-serif; font-size: 12px; font-weight: bold; }

        .editor-container { background: white; border: 2px solid var(--gold); padding: 30px; margin-top: 20px; border-radius: 8px; font-family: sans-serif; }
        .form-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; }
        input, textarea { width: 100%; padding: 10px; margin: 5px 0 10px; border: 1px solid #ccc; border-radius: 4px; box-sizing: border-box; }
        .btn-post { background: #28a745; color: white; border: none; padding: 18px; width: 100%; font-weight: bold; cursor: pointer; font-size: 16px; border-radius: 4px; }
        .btn-del { background: #dc3545; color: white; border: none; padding: 5px 10px; cursor: pointer; border-radius: 3px; font-size: 11px; }

        .tab { display: none; }
        .active { display: block; }
        .archive-item { background: white; padding: 15px; border: 1px solid #ddd; margin-bottom: 10px; display: flex; justify-content: space-between; align-items: center; }
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
    <div class="nav-btn" id="n-logs" style="display:none;" onclick="switchTab('logs')">ЛОГИ</div>
    <div class="nav-btn" style="border-color:#5cb85c" onclick="openLogin()">ВХОД</div>
</nav>

<div class="container">
    <div id="tab-main" class="tab active">
        <div class="paper" id="report-view"></div>
    </div>

    <div id="tab-archive" class="tab">
        <h2 style="margin-top:40px;">Архив департамента</h2>
        <div id="archive-list"></div>
    </div>

    <div id="tab-editor" class="tab">
        <div class="editor-container">
            <h2>Создать новый развернутый отчет</h2>
            <label>Период:</label><input type="text" id="f-period" value="05.04.2026 — 12.04.2026">
            
            <div class="form-grid">
                <div>
                    <h3>Аппарат Правительства</h3>
                    <label>Всего лицензий:</label><input type="number" id="f-lic-all" value="0">
                    <label>На оружие:</label><input type="number" id="f-lic-gun" value="0">
                    <label>На охоту:</label><input type="number" id="f-lic-hunt" value="0">
                    <label>На рыбалку:</label><input type="number" id="f-lic-fish" value="0">
                    <label>Собеседования:</label><input type="number" id="f-int" value="0">
                    <label>Стажировки:</label><input type="number" id="f-staj" value="0">
                    <label>Экзамены:</label><input type="number" id="f-exam" value="0">
                    <label>Премии (Имя|%):</label>
                    <textarea id="f-app-bonus" rows="3" placeholder="Имя|60%"></textarea>
                </div>
                <div>
                    <h3>Здравоохранение</h3>
                    <label>Надзорные дела:</label><input type="number" id="f-nadz" value="0">
                    <label>Мед. проверки:</label><input type="number" id="f-med" value="0">
                    <label>Собесы в EMS:</label><input type="number" id="f-ems-int" value="0">
                    <label>Штрафы ($):</label><input type="text" id="f-sh" value="0">
                    <label>Акции:</label><input type="number" id="f-ak" value="0">
                    <label>Премии (Имя|%):</label>
                    <textarea id="f-med-bonus" rows="3" placeholder="Имя|50%"></textarea>
                </div>
            </div>
            <label>Ссылки на отчеты (Название|Ссылка):</label>
            <textarea id="f-links" rows="2" placeholder="Документ|https://..."></textarea>
            
            <button class="btn-post" onclick="publish()">ОПУБЛИКОВАТЬ В СИСТЕМУ</button>
        </div>
    </div>

    <div id="tab-users" class="tab">
        <div class="editor-container">
            <h3>Управление персоналом</h3>
            <input type="text" id="u-name" placeholder="Имя Фамилия">
            <input type="email" id="u-email" placeholder="Email">
            <input type="password" id="u-pass" placeholder="Пароль">
            <button class="btn-post" style="background:var(--primary)" onclick="addUser()">ДОБАВИТЬ</button>
            <div id="user-list" style="margin-top:20px;"></div>
        </div>
    </div>

    <div id="tab-logs" class="tab">
        <div class="editor-container">
            <h3>История системы</h3>
            <div id="log-list" style="background:#f9f9f9; padding:10px; max-height:400px; overflow-y:auto;"></div>
        </div>
    </div>
</div>

<div id="login-modal" style="display:none; position:fixed; top:50%; left:50%; transform:translate(-50%,-50%); background:white; padding:40px; border-top:10px solid var(--primary); z-index:1001; box-shadow:0 0 100px rgba(0,0,0,0.5);">
    <h3>Вход в архив</h3>
    <input type="email" id="l-email" placeholder="Email">
    <input type="password" id="l-pass" placeholder="Пароль">
    <button class="btn-post" onclick="login()">ВОЙТИ</button>
</div>

<script>
    let db = JSON.parse(localStorage.getItem('dol_full_v9')) || {
        users: [{email: 'minister@dol.gov', pass: 'Laba2026', name: 'Artur Drovic', role: 'Admin'}],
        reports: [{
            id: Date.now(),
            meta: "Экземпляр №1", period: "05.04.2026 — 12.04.2026",
            data: {
                lic_all: 315, lic_gun: 239, lic_hunt: 40, lic_fish: 36,
                int: 6, staj: 47, exam: 40, nadz: 22, med: 4, ems_int: 6, sh: "280.000", ak: 3
            },
            bonus_app: "Mops Sergeyevich|60%\nJamess Soprano|40%",
            bonus_med: "Dia Dema|50%\nDis Morales|40%",
            links: "Google Drive|#"
        }],
        logs: []
    };

    let currentUser = null;

    function save() { localStorage.setItem('dol_full_v9', JSON.stringify(db)); }

    function addLog(msg) {
        db.logs.unshift(`[${new Date().toLocaleString()}] ${currentUser ? currentUser.name : 'System'}: ${msg}`);
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
        const parseBonus = (txt) => txt.split('\n').filter(l => l.includes('|')).map(l => {
            let [n, p] = l.split('|'); return `<tr><td>${n}</td><td style="text-align:right"><span class="pct">${p}</span></td></tr>`;
        }).join('');

        const linksHtml = r.links.split('\n').filter(l => l.includes('|')).map(l => {
            let [n, u] = l.split('|'); return `<a href="${u}" target="_blank" style="color:blue; display:block; margin:5px 0;">📄 ${n}</a>`;
        }).join('');

        document.getElementById('report-view').innerHTML = `
            <div style="text-align:right; font-style:italic; color:gray; font-size:12px;">${r.meta}</div>
            <p>Кому: <b>Губернатору Anna Moroz</b><br>От: <b>Министра Artur Drovic</b></p>
            <h2>Еженедельный отчет Министерства Труда</h2>
            <p style="text-align:center;">Отчетный период: <b>${r.period}</b></p>
            
            <h3>1. Министерство (Премии)</h3>
            <div class="row-split">
                <table><tr><td>Artur Drovic (Министр)</td></tr><tr><td>Miy Li (Зам)</td></tr><tr><td>Elmer Moroz (Зам)</td></tr></table>
                <table><tr><td style="text-align:right"><span class="pct">34%</span></td></tr><tr><td style="text-align:right"><span class="pct">33%</span></td></tr><tr><td style="text-align:right"><span class="pct">33%</span></td></tr></table>
            </div>

            <h3>2. Аппарат Правительства</h3>
            <div class="row-split">
                <table>
                    <tr><td>Выдано лицензий (всего)</td><td><b>${r.data.lic_all}</b></td></tr>
                    <tr><td>Лицензии на оружие</td><td>${r.data.lic_gun}</td></tr>
                    <tr><td>Лицензии на охоту</td><td>${r.data.lic_hunt}</td></tr>
                    <tr><td>Лицензии на рыбалку</td><td>${r.data.lic_fish}</td></tr>
                    <tr><td>Собеседования</td><td>${r.data.int}</td></tr>
                    <tr><td>Стажировки</td><td>${r.data.staj}</td></tr>
                    <tr><td>Экзамены</td><td>${r.data.exam}</td></tr>
                </table>
                <table>
                    <tr><th colspan="2">Премии Аппарата</th></tr>
                    ${parseBonus(r.bonus_app) || '<tr><td>Нет данных</td></tr>'}
                </table>
            </div>

            <h3>3. Департамент Здравоохранения</h3>
            <div class="row-split">
                <table>
                    <tr><td>Надзорные дела</td><td>${r.data.nadz}</td></tr>
                    <tr><td>Медицинские проверки</td><td>${r.data.med}</td></tr>
                    <tr><td>Собеседования в EMS</td><td>${r.data.ems_int}</td></tr>
                    <tr><td>Общая сумма штрафов</td><td><b>${r.data.sh}$</b></td></tr>
                    <tr><td>Благотворительные акции</td><td>${r.data.ak}</td></tr>
                </table>
                <table>
                    <tr><th colspan="2">Премии Здравоохранения</th></tr>
                    ${parseBonus(r.bonus_med) || '<tr><td>Нет данных</td></tr>'}
                </table>
            </div>

            <h3>Прикрепленные ссылки</h3>
            <div style="background:#f9f9f9; padding:15px; border:1px dashed #ccc;">${linksHtml || 'Ссылки отсутствуют'}</div>
        `;
    }

    function publish() {
        let newR = {
            id: Date.now(),
            meta: `Экземпляр №${db.reports.length + 1}`,
            period: document.getElementById('f-period').value,
            data: {
                lic_all: document.getElementById('f-lic-all').value,
                lic_gun: document.getElementById('f-lic-gun').value,
                lic_hunt: document.getElementById('f-lic-hunt').value,
                lic_fish: document.getElementById('f-lic-fish').value,
                int: document.getElementById('f-int').value,
                staj: document.getElementById('f-staj').value,
                exam: document.getElementById('f-exam').value,
                nadz: document.getElementById('f-nadz').value,
                med: document.getElementById('f-med').value,
                ems_int: document.getElementById('f-ems-int').value,
                sh: document.getElementById('f-sh').value,
                ak: document.getElementById('f-ak').value
            },
            bonus_app: document.getElementById('f-app-bonus').value,
            bonus_med: document.getElementById('f-med-bonus').value,
            links: document.getElementById('f-links').value
        };
        db.reports.unshift(newR);
        addLog("Создан отчет за " + newR.period);
        save();
        switchTab('main');
        renderReport(db.reports[0]);
    }

    function renderArchive() {
        document.getElementById('archive-list').innerHTML = db.reports.map((r, i) => `
            <div class="archive-item">
                <div style="cursor:pointer; flex-grow:1;" onclick="renderReport(db.reports[${i}]); switchTab('main')">
                    <b>${r.period}</b> <small>(${r.meta})</small>
                </div>
                ${currentUser ? `<button class="btn-del" onclick="deleteReport(${r.id})">УДАЛИТЬ</button>` : ''}
            </div>
        `).join('');
    }

    function deleteReport(id) {
        if(confirm("Вы уверены, что хотите удалить этот отчет из архива?")) {
            db.reports = db.reports.filter(x => x.id !== id);
            addLog("Отчет удален");
            save();
            renderArchive();
        }
    }

    function login() {
        let u = db.users.find(x => x.email === document.getElementById('l-email').value && x.pass === document.getElementById('l-pass').value);
        if(u) {
            currentUser = u;
            document.getElementById('n-edit').style.display='block';
            document.getElementById('n-logs').style.display='block';
            if(u.role==='Admin') document.getElementById('n-users').style.display='block';
            addLog("Успешная авторизация");
            document.getElementById('login-modal').style.display='none';
            renderArchive();
        } else { alert("Ошибка доступа!"); }
    }

    function openLogin() { document.getElementById('login-modal').style.display='block'; }
    function renderLogs() { document.getElementById('log-list').innerHTML = db.logs.map(l => `<div style="font-size:12px; border-bottom:1px solid #eee; padding:3px;">${l}</div>`).join(''); }
    function renderUsers() { document.getElementById('user-list').innerHTML = db.users.map(u => `<div>${u.name} (${u.role})</div>`).join(''); }
    function addUser() {
        db.users.push({name: document.getElementById('u-name').value, email: document.getElementById('u-email').value, pass: document.getElementById('u-pass').value, role: 'Editor'});
        addLog("Добавлен сотрудник: " + document.getElementById('u-name').value); 
        save(); renderUsers();
    }

    renderReport(db.reports[0]);
</script>
</body>
</html>
