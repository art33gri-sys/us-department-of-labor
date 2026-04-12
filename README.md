<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>U.S. Department of Labor | Federal Management</title>
    <style>
        /* ИНТЕРЬЕР И ДИЗАЙН СОХРАНЕНЫ */
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
        table th, table td { padding: 10px; border: 1px solid #ddd; text-align: left; }
        .pct { background: var(--primary); color: white; padding: 2px 6px; border-radius: 3px; font-family: sans-serif; font-size: 12px; }

        .editor-container { background: white; border: 2px solid var(--gold); padding: 30px; margin-top: 20px; border-radius: 8px; font-family: sans-serif; }
        .form-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; }
        input, textarea { width: 100%; padding: 10px; margin: 5px 0 12px; border: 1px solid #ccc; border-radius: 4px; box-sizing: border-box; }
        .btn-post { background: #28a745; color: white; border: none; padding: 15px; width: 100%; font-weight: bold; cursor: pointer; font-size: 16px; border-radius: 4px; }

        .tab { display: none; }
        .active { display: block; }
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
        <div class="paper" id="current-report-content"></div>
    </div>

    <div id="tab-archive" class="tab">
        <h2 style="margin-top:40px;">Архив департамента</h2>
        <div id="archive-list"></div>
    </div>

    <div id="tab-editor" class="tab">
        <div class="editor-container">
            <h2>Новый еженедельный отчет</h2>
            <label>Период:</label><input type="text" id="in-period" placeholder="05.04.2026 — 12.04.2026">

            <div class="form-grid">
                <div>
                    <h3>Аппарат Правительства</h3>
                    <label>Лицензии (всего):</label><input type="number" id="in-lic" value="0">
                    <label>На оружие:</label><input type="number" id="in-gun" value="0">
                    <label>Охота / Рыбалка:</label><input type="text" id="in-hunt" value="0 / 0">
                    <label>Собеседования:</label><input type="number" id="in-int" value="0">
                    <label>Стажировки:</label><input type="number" id="in-staj" value="0">
                    <label>Экзамены:</label><input type="number" id="in-exam" value="0">
                    <label>Лекции:</label><input type="number" id="in-lect" value="0">
                </div>
                <div>
                    <h3>Здравоохранение</h3>
                    <label>Надзорные дела:</label><input type="number" id="in-nadz" value="0">
                    <label>Мед. проверки:</label><input type="number" id="in-med" value="0">
                    <label>Собесы в EMS:</label><input type="number" id="in-ems-int" value="0">
                    <label>Нарушения в EMS:</label><input type="number" id="in-ems-warn" value="0">
                    <label>Штрафы ($):</label><input type="text" id="in-sh" value="0">
                    <label>Благотв. Акции:</label><input type="number" id="in-ak" value="0">
                </div>
            </div>

            <label>Ссылки на отчеты (Название|Ссылка):</label>
            <textarea id="in-links" rows="3" placeholder="Google Drive|https://..."></textarea>

            <button class="btn-post" onclick="createNewReport()">Опубликовать отчет</button>
        </div>
    </div>

    <div id="tab-users" class="tab">
        <div class="editor-container">
            <h3>Добавить сотрудника</h3>
            <input type="text" id="u-name" placeholder="Имя Фамилия">
            <input type="email" id="u-email" placeholder="Email">
            <input type="password" id="u-pass" placeholder="Пароль">
            <button class="btn-post" style="background:var(--primary)" onclick="saveUser()">Добавить</button>
            <div id="user-list-display" style="margin-top:20px;"></div>
        </div>
    </div>
</div>

<div id="login-modal" style="display:none; position:fixed; top:50%; left:50%; transform:translate(-50%,-50%); background:white; padding:40px; border-top:10px solid var(--primary); z-index:1001; box-shadow:0 0 100px rgba(0,0,0,0.5);">
    <h3>Вход в систему</h3>
    <input type="email" id="l-email" placeholder="Email">
    <input type="password" id="l-pass" placeholder="Пароль">
    <button class="btn-post" onclick="doLogin()">Войти</button>
    <center><button onclick="closeLogin()" style="background:none; border:none; color:gray; cursor:pointer; margin-top:15px;">Отмена</button></center>
</div>

<script>
    let data = JSON.parse(localStorage.getItem('dol_final_v5')) || {
        users: [{email: 'minister@dol.gov', pass: 'Laba2026', name: 'Artur Drovic', role: 'Admin'}],
        reports: [{
            id: 1,
            meta: "Экземпляр №1 | 12.04.2026",
            period: "05.04.2026 — 12.04.2026",
            content: {lic: 315, gun: 239, hunt: "33 / 33", int: 6, staj: 47, exam: 40, lect: 33, nadz: 22, med: 4, ems_int: 6, ems_warn: 0, sh: "280.000", ak: 3},
            links: "Пример отчета|#"
        }]
    };

    let currentUser = null;

    function saveDB() { localStorage.setItem('dol_final_v5', JSON.stringify(data)); }

    function switchTab(t) {
        document.querySelectorAll('.tab').forEach(x => x.classList.remove('active'));
        document.getElementById('tab-' + t).classList.add('active');
        if(t === 'archive') renderArchive();
        if(t === 'users') renderUserList();
    }

    function renderReport(report) {
        let linksHtml = report.links.split('\n').filter(l => l.includes('|')).map(l => {
            let [n, u] = l.split('|'); return `<a href="${u}" target="_blank" style="color:#0056b3; display:block; margin:5px 0;">📄 ${n}</a>`;
        }).join('');

        document.getElementById('current-report-content').innerHTML = `
            <div style="text-align:right; font-style:italic; color:gray;">${report.meta}</div>
            <p>Кому: <b>Губернатору Anna Moroz</b><br>От: <b>Министра Artur Drovic</b></p>
            <h2>Еженедельный отчет Министерства Труда</h2>
            <p style="text-align:center;">Период: <b>${report.period}</b></p>
            
            <h3>1. Министерство (Аудит)</h3>
            <table>
                <tr><td>Artur Drovic (Министр)</td><td><span class="pct">34%</span></td></tr>
                <tr><td>Miy Li (Зам)</td><td><span class="pct">33%</span></td></tr>
                <tr><td>Elmer Moroz (Зам)</td><td><span class="pct">33%</span></td></tr>
            </table>

            <h3>2. Аппарат Правительства</h3>
            <table>
                <tr><td>Лицензий выдано (Всего)</td><td><b>${report.content.lic}</b></td></tr>
                <tr><td>Лицензии на оружие</td><td>${report.content.gun}</td></tr>
                <tr><td>Охота / Рыбалка</td><td>${report.content.hunt}</td></tr>
                <tr><td>Проведено собеседований</td><td>${report.content.int}</td></tr>
                <tr><td>Принято на стажировку</td><td>${report.content.staj}</td></tr>
                <tr><td>Принято экзаменов</td><td>${report.content.exam}</td></tr>
                <tr><td>Прочитано лекций</td><td>${report.content.lect}</td></tr>
            </table>

            <h3>3. Департамент Здравоохранения</h3>
            <table>
                <tr><td>Надзорные мероприятия</td><td>${report.content.nadz}</td></tr>
                <tr><td>Медицинские проверки</td><td>${report.content.med}</td></tr>
                <tr><td>Собеседования в EMS</td><td>${report.content.ems_int}</td></tr>
                <tr><td>Нарушения в EMS</td><td><b>${report.content.ems_warn}</b></td></tr>
                <tr><td>Общая сумма штрафов</td><td><b>${report.content.sh}$</b></td></tr>
                <tr><td>Благотворительные акции</td><td>${report.content.ak}</td></tr>
            </table>

            <h3>Прикрепленные ссылки</h3>
            <div style="background:#f8f9fa; padding:15px; border:1px dashed #ccc;">${linksHtml || 'Нет ссылок'}</div>
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
                hunt: document.getElementById('in-hunt').value,
                int: document.getElementById('in-int').value,
                staj: document.getElementById('in-staj').value,
                exam: document.getElementById('in-exam').value,
                lect: document.getElementById('in-lect').value,
                nadz: document.getElementById('in-nadz').value,
                med: document.getElementById('in-med').value,
                ems_int: document.getElementById('in-ems-int').value,
                ems_warn: document.getElementById('in-ems-warn').value,
                sh: document.getElementById('in-sh').value,
                ak: document.getElementById('in-ak').value
            },
            links: document.getElementById('in-links').value
        };
        data.reports.unshift(newR);
        saveDB();
        switchTab('main');
        renderReport(data.reports[0]);
    }

    function renderArchive() {
        document.getElementById('archive-list').innerHTML = data.reports.map((r, i) => `
            <div style="background:white; padding:15px; margin-bottom:10px; border:1px solid #ddd; cursor:pointer; display:flex; justify-content:space-between;">
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
            saveDB(); renderArchive();
        }
    }

    function openLogin() { document.getElementById('login-modal').style.display='block'; }
    function closeLogin() { document.getElementById('login-modal').style.display='none'; }

    function doLogin() {
        let u = data.users.find(x => x.email === document.getElementById('l-email').value && x.pass === document.getElementById('l-pass').value);
        if(u) {
            currentUser = u;
            document.getElementById('n-edit').style.display = 'block';
            if(u.role === 'Admin') document.getElementById('n-users').style.display = 'block';
            closeLogin();
        } else { alert("Ошибка!"); }
    }

    function saveUser() {
        data.users.push({name: document.getElementById('u-name').value, email: document.getElementById('u-email').value, pass: document.getElementById('u-pass').value, role: 'Editor'});
        saveDB(); renderUserList();
    }

    function renderUserList() {
        document.getElementById('user-list-display').innerHTML = data.users.map(u => `<div>${u.name} (${u.role})</div>`).join('');
    }

    renderReport(data.reports[0]);
</script>
</body>
</html>
