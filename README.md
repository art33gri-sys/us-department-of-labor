<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>U.S. Department of Labor | Federal Archive</title>
    <style>
        :root { --primary: #002e6d; --accent: #cd2026; --gold: #b69156; --bg: #f4f4f4; }
        body { font-family: 'Times New Roman', serif; margin: 0; background: var(--bg); color: #333; line-height: 1.5; }
        
        header { background: var(--primary); color: white; padding: 20px 0; border-bottom: 4px solid var(--accent); text-align: center; }
        .container { max-width: 1000px; margin: 0 auto; padding: 0 20px; }
        
        nav { background: #112e51; padding: 10px 0; display: flex; justify-content: center; gap: 15px; position: sticky; top: 0; z-index: 100; }
        .nav-btn { text-decoration: none; color: white; font-weight: bold; padding: 8px 15px; border: 2px solid var(--gold); border-radius: 4px; font-family: sans-serif; cursor: pointer; font-size: 12px; }
        .nav-btn:hover { background: var(--gold); color: black; }

        .paper { background: white; padding: 60px; margin: 30px auto; box-shadow: 0 0 20px rgba(0,0,0,0.2); border: 1px solid #ccc; }
        h2 { text-align: center; color: var(--primary); border-bottom: 2px solid var(--primary); margin-bottom: 30px; }
        h3 { background: #eee; padding: 10px; border-left: 6px solid var(--primary); color: var(--primary); margin-top: 30px; text-transform: uppercase; font-size: 18px; }

        table { width: 100%; border-collapse: collapse; margin: 15px 0; }
        table th, table td { padding: 12px; border: 1px solid #ddd; text-align: left; }
        table th { background: #f9f9f9; font-weight: bold; }
        .pct { background: var(--primary); color: white; padding: 2px 6px; border-radius: 3px; font-family: sans-serif; font-size: 12px; }

        .admin-section { display: none; background: white; border: 2px solid var(--gold); padding: 30px; margin-top: 20px; border-radius: 8px; font-family: sans-serif; }
        .grid { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; }
        input, textarea, select { width: 100%; padding: 10px; margin: 5px 0 15px; border: 1px solid #ccc; border-radius: 4px; box-sizing: border-box; }
        .btn-save { background: #28a745; color: white; border: none; padding: 15px; width: 100%; font-weight: bold; cursor: pointer; font-size: 16px; }

        .tab { display: none; }
        .active { display: block; }
        .log-entry { font-family: monospace; font-size: 11px; padding: 4px; border-bottom: 1px solid #ddd; }
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
    <div class="nav-btn" onclick="showTab('main')">ГЛАВНАЯ</div>
    <div class="nav-btn" onclick="showTab('archive')">АРХИВ</div>
    <div class="nav-btn" id="n-edit" style="display:none;" onclick="showTab('editor')">СОЗДАТЬ ОТЧЕТ</div>
    <div class="nav-btn" id="n-users" style="display:none;" onclick="showTab('users')">ПЕРСОНАЛ</div>
    <div class="nav-btn" id="n-logs" style="display:none;" onclick="showTab('logs')">ЛОГИ</div>
    <div class="nav-btn" style="border-color:#5cb85c" onclick="openLogin()">ВХОД</div>
</nav>

<div class="container">
    <div id="tab-main" class="tab active">
        <div class="paper" id="main-content"></div>
    </div>

    <div id="tab-archive" class="tab">
        <h2 style="margin-top:40px;">Архив департамента</h2>
        <div id="archive-list"></div>
    </div>

    <div id="tab-editor" class="tab">
        <div class="admin-section">
            <h3>Генерация полного отчета</h3>
            <div class="grid">
                <div>
                    <label>Период:</label><input type="text" id="p-range" value="05.04.2026 — 12.05.2026">
                    <h4>Аппарат Правительства (Данные)</h4>
                    <label>Лицензии (Всего):</label><input type="number" id="p-lic" value="315">
                    <label>Оружие:</label><input type="number" id="p-gun" value="239">
                    <label>Охота / Рыбалка:</label><input type="text" id="p-hunt" value="33 / 33">
                    <label>Собеседования:</label><input type="number" id="p-int" value="6">
                    <label>Стажировки (Принято):</label><input type="number" id="p-staj" value="47">
                    <label>Экзамены / Лекции:</label><input type="text" id="p-exam" value="40 / 33">
                </div>
                <div>
                    <h4>Департамент Здравоохранения (Данные)</h4>
                    <label>Надзорные деятельностей:</label><input type="number" id="p-nadz" value="22">
                    <label>Мед. проверки:</label><input type="number" id="p-med" value="4">
                    <label>Собеседования в EMS:</label><input type="number" id="p-ems" value="6">
                    <label>Нарушения в EMS:</label><input type="number" id="p-warn" value="0">
                    <label>Благотворительные акции:</label><input type="number" id="p-char" value="3">
                    <label>Сумма вознаграждений ($):</label><input type="text" id="p-ak-val" value="101.000">
                    <label>Общая сумма штрафов ($):</label><input type="text" id="p-sh-val" value="280.000">
                </div>
            </div>
            <button class="btn-save" onclick="submitReport()">ОПУБЛИКОВАТЬ ПОЛНЫЙ ОТЧЕТ</button>
        </div>
    </div>

    <div id="tab-users" class="tab">
        <div class="admin-section">
            <h3>Управление сотрудниками</h3>
            <div id="u-list"></div>
            <hr>
            <h4>Добавить сотрудника</h4>
            <input type="text" id="u-new-name" placeholder="Имя Фамилия">
            <input type="email" id="u-new-email" placeholder="Email">
            <input type="password" id="u-new-pass" placeholder="Пароль">
            <button class="btn-save" style="background:var(--primary)" onclick="addUser()">ДОБАВИТЬ</button>
        </div>
    </div>

    <div id="tab-logs" class="tab">
        <div class="admin-section">
            <h3>Логи действий</h3>
            <div id="l-container"></div>
        </div>
    </div>
</div>

<div id="login-modal" style="display:none; position:fixed; top:50%; left:50%; transform:translate(-50%,-50%); background:white; padding:30px; border-top:10px solid var(--primary); z-index:1001; box-shadow:0 0 50px rgba(0,0,0,0.5);">
    <h3 style="margin-top:0;">Системный вход</h3>
    <input type="email" id="li-e" placeholder="Email">
    <input type="password" id="li-p" placeholder="Пароль">
    <button class="btn-save" onclick="login()">ВОЙТИ</button>
    <button onclick="closeLogin()" style="border:none; background:none; color:gray; cursor:pointer; width:100%; margin-top:10px;">Отмена</button>
</div>

<script>
    let storage = JSON.parse(localStorage.getItem('dol_vfinal')) || {
        users: [{email: 'minister@dol.gov', pass: 'Laba2026', name: 'Artur Drovic', role: 'Admin'}],
        reports: [{
            meta: "Экземпляр №1 | 12.05.2026",
            period: "05.04.2026 — 12.05.2026",
            data: { lic: 315, gun: 239, hunt: "33 / 33", int: 6, staj: 47, exam: "40 / 33", nadz: 22, med: 4, ems: 6, warn: 0, char: 3, ak: "101.000", sh: "280.000" }
        }],
        logs: []
    };

    let currentUser = null;

    function save() { localStorage.setItem('dol_vfinal', JSON.stringify(storage)); }

    function addLog(m) {
        storage.logs.unshift(`[${new Date().toLocaleString()}] ${currentUser ? currentUser.name : 'System'}: ${m}`);
        save();
    }

    function showTab(t) {
        document.querySelectorAll('.tab').forEach(x => x.classList.remove('active'));
        document.getElementById('tab-' + t).classList.add('active');
        if(t === 'archive') renderArchive();
        if(t === 'users') renderUsers();
        if(t === 'logs') renderLogs();
    }

    function renderReport(r) {
        document.getElementById('main-content').innerHTML = `
            <div style="font-style:italic; color:gray;">${r.meta}</div>
            <h2>Еженедельный отчет Министерства Труда</h2>
            <p style="text-align:center;">Период: <b>${r.period}</b></p>
            
            <h3>1. Кадровый аудит и премии (Министерство)</h3>
            <table>
                <tr><th>Сотрудник</th><th>Доля премии</th></tr>
                <tr><td>Artur Drovic (Министр)</td><td><span class="pct">34%</span></td></tr>
                <tr><td>Miy Li (Зам. Министра)</td><td><span class="pct">33%</span></td></tr>
                <tr><td>Elmer Moroz (Зам. Министра)</td><td><span class="pct">33%</span></td></tr>
            </table>

            <h3>2. Аппарат Правительства</h3>
            <table>
                <tr><td>Выдано лицензий (Всего)</td><td><b>${r.data.lic}</b></td></tr>
                <tr><td>Лицензии на оружие</td><td>${r.data.gun}</td></tr>
                <tr><td>Охота / Рыбалка</td><td>${r.data.hunt}</td></tr>
                <tr><td>Проведено собеседований</td><td>${r.data.int}</td></tr>
                <tr><td>Принято на стажировку</td><td>${r.data.staj}</td></tr>
                <tr><td>Экзамены / Лекции</td><td>${r.data.exam}</td></tr>
            </table>
            <h4>Список на премирование (Аппарат)</h4>
            <table>
                <tr><td>Mops Sergeyevich (Глава)</td><td><span class="pct">60%</span></td></tr>
                <tr><td>Jamess Soprano (Зам. Главы)</td><td><span class="pct">40%</span></td></tr>
            </table>

            <h3>3. Департамент Здравоохранения</h3>
            <table>
                <tr><td>Надзорные деятельностей</td><td>${r.data.nadz}</td></tr>
                <tr><td>Мед. проверки / Собеседования в EMS</td><td>${r.data.med} / ${r.data.ems}</td></tr>
                <tr><td>Выявлено нарушений сотрудников EMS</td><td><b>${r.data.warn}</b></td></tr>
                <tr><td>Благотворительные акции</td><td>${r.data.char}</td></tr>
                <tr><td>Сумма вознаграждений за акции</td><td><b style="color:green;">${r.data.ak}$</b></td></tr>
                <tr><td>Общая сумма штрафов</td><td><b style="color:red;">${r.data.sh}$</b></td></tr>
            </table>
            <h4>Список на премирование (Здравоохранение)</h4>
            <table>
                <tr><td>Dia Dema (Директор)</td><td><span class="pct">50%</span></td></tr>
                <tr><td>Dis Morales (Зам. Директора)</td><td><span class="pct">40%</span></td></tr>
                <tr><td>Luigi Cudi (Зам. Директора)</td><td><span class="pct">10%</span></td></tr>
            </table>
        `;
    }

    function submitReport() {
        let newR = {
            meta: `Экземпляр №${storage.reports.length + 1} | ${new Date().toLocaleDateString()}`,
            period: document.getElementById('p-range').value,
            data: {
                lic: document.getElementById('p-lic').value,
                gun: document.getElementById('p-gun').value,
                hunt: document.getElementById('p-hunt').value,
                int: document.getElementById('p-int').value,
                staj: document.getElementById('p-staj').value,
                exam: document.getElementById('p-exam').value,
                nadz: document.getElementById('p-nadz').value,
                med: document.getElementById('p-med').value,
                ems: document.getElementById('p-ems').value,
                warn: document.getElementById('p-warn').value,
                char: document.getElementById('p-char').value,
                ak: document.getElementById('p-ak-val').value,
                sh: document.getElementById('p-sh-val').value
            }
        };
        storage.reports.unshift(newR);
        addLog(`Создан отчет за период ${newR.period}`);
        save();
        location.reload();
    }

    function renderArchive() {
        document.getElementById('archive-list').innerHTML = storage.reports.map((r, i) => `
            <div style="background:white; padding:15px; margin-bottom:10px; border:1px solid #ddd; cursor:pointer;" onclick="renderReport(storage.reports[${i}]); showTab('main')">
                <b>${r.period}</b><br><small>${r.meta}</small>
            </div>
        `).join('');
    }

    function renderUsers() {
        document.getElementById('u-list').innerHTML = storage.users.map((u, i) => `
            <div style="padding:10px; border-bottom:1px solid #eee;">${u.name} (${u.email}) - <b>${u.role}</b></div>
        `).join('');
    }

    function renderLogs() { document.getElementById('l-container').innerHTML = storage.logs.map(l => `<div class="log-entry">${l}</div>`).join(''); }

    function openLogin() { document.getElementById('login-modal').style.display='block'; }
    function closeLogin() { document.getElementById('login-modal').style.display='none'; }

    function login() {
        let e = document.getElementById('li-e').value;
        let p = document.getElementById('li-p').value;
        let u = storage.users.find(x => x.email === e && x.pass === p);
        if(u) {
            currentUser = u;
            document.getElementById('n-edit').style.display='block';
            if(u.role === 'Admin') { document.getElementById('n-users').style.display='block'; document.getElementById('n-logs').style.display='block'; }
            addLog("Авторизация");
            closeLogin();
        } else { alert("Ошибка доступа"); }
    }

    function addUser() {
        storage.users.push({name: document.getElementById('u-new-name').value, email: document.getElementById('u-new-email').value, pass: document.getElementById('u-new-pass').value, role: 'Editor'});
        addLog("Добавлен новый пользователь");
        save();
        renderUsers();
    }

    // Инициализация
    renderReport(storage.reports[0]);
</script>
</body>
</html>
