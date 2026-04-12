<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>U.S. Department of Labor | Official Archive</title>
    <style>
        :root {
            --primary: #002e6d;
            --accent: #cd2026;
            --gold: #b69156;
            --bg: #e9ecef;
        }

        body { font-family: 'Times New Roman', serif; margin: 0; background: var(--bg); color: #333; }
        
        /* Шапка по красоте */
        header { background: var(--primary); color: white; padding: 20px 0; border-bottom: 4px solid var(--accent); }
        .container { max-width: 1100px; margin: 0 auto; padding: 0 20px; }
        .header-content { display: flex; align-items: center; justify-content: space-between; }
        .logo-box { display: flex; align-items: center; gap: 20px; }
        .logo-box img { width: 90px; height: 90px; }

        /* Заметные кнопки навигации */
        nav { background: #112e51; padding: 15px 0; box-shadow: 0 4px 6px rgba(0,0,0,0.2); }
        .nav-flex { display: flex; gap: 15px; justify-content: center; }
        .nav-btn { 
            text-decoration: none; color: white; font-weight: bold; padding: 10px 25px; 
            border: 2px solid var(--gold); border-radius: 4px; font-family: sans-serif; 
            text-transform: uppercase; cursor: pointer; transition: 0.3s; background: rgba(255,255,255,0.05);
        }
        .nav-btn:hover { background: var(--gold); color: black; }
        .btn-auth { border-color: #5cb85c; }

        /* Лист отчета */
        .report-paper { 
            background: white; padding: 60px; margin: 40px auto; max-width: 900px;
            box-shadow: 0 0 30px rgba(0,0,0,0.15); border: 1px solid #ccc; position: relative;
        }
        .stamp-meta { position: absolute; top: 20px; left: 60px; font-style: italic; color: #777; font-size: 14px; }
        
        h2 { text-align: center; color: var(--primary); text-transform: uppercase; border-bottom: 2px solid var(--primary); padding-bottom: 10px; }
        h3 { background: #f8f9fa; padding: 10px; border-left: 5px solid var(--primary); margin-top: 35px; color: var(--primary); }

        table { width: 100%; border-collapse: collapse; margin: 15px 0; }
        table td { padding: 12px; border-bottom: 1px solid #eee; }
        .label { font-weight: bold; width: 60%; }
        .pct { background: var(--primary); color: white; padding: 3px 8px; border-radius: 3px; font-size: 13px; font-family: sans-serif; }

        /* Админка */
        #editor-panel { display: none; background: #fff3cd; padding: 25px; border: 2px dashed #856404; margin-bottom: 20px; border-radius: 8px; }
        textarea { width: 100%; padding: 10px; margin: 10px 0; border: 1px solid #ccc; border-radius: 4px; }
        .save-btn { background: #28a745; color: white; padding: 15px; border: none; width: 100%; font-weight: bold; cursor: pointer; border-radius: 4px; }

        /* Архив */
        #archive-box { display: none; }
        .archive-card { background: white; padding: 20px; margin-bottom: 15px; border: 1px solid #ddd; border-left: 8px solid var(--gold); cursor: pointer; }

        .clickable-docs a { color: #0056b3; text-decoration: underline; font-weight: bold; display: block; margin: 5px 0; }
    </style>
</head>
<body>

<header>
    <div class="container header-content">
        <div class="logo-box">
            <img src="https://upload.wikimedia.org/wikipedia/commons/d/da/Seal_of_the_United_States_Department_of_Labor.svg" alt="Seal">
            <div>
                <h1 style="margin:0;">U.S. DEPARTMENT OF LABOR</h1>
                <p style="margin:0; opacity: 0.8; font-family: sans-serif;">Secretary of Labor Reporting Authority</p>
            </div>
        </div>
    </div>
</header>

<nav>
    <div class="container nav-flex">
        <div class="nav-btn" onclick="showTab('home')">ГЛАВНАЯ СТРАНИЦА</div>
        <div class="nav-btn" onclick="showTab('archive')">АРХИВ ОТЧЕТОВ</div>
        <div class="nav-btn btn-auth" id="loginLabel" onclick="openLogin()">ВХОД В СИСТЕМУ</div>
    </div>
</nav>

<div class="container">
    
    <div id="editor-panel">
        <h3 style="margin-top:0;">ПАНЕЛЬ УПРАВЛЕНИЯ ОТЧЕТАМИ</h3>
        <input type="text" id="in-meta" placeholder="Экземпляр №... | Дата..." style="width:100%; padding:10px; margin-bottom:10px;">
        <input type="text" id="in-period" placeholder="Период (05.04.2026 - 12.05.2026)" style="width:100%; padding:10px; margin-bottom:10px;">
        <textarea id="in-content" rows="10" placeholder="Вставьте данные отчета (можно использовать HTML теги)"></textarea>
        <textarea id="in-links" rows="3" placeholder="Название|Ссылка (каждая с новой строки)"></textarea>
        <button class="save-btn" onclick="publishReport()">ОПУБЛИКОВАТЬ В СИСТЕМУ</button>
    </div>

    <div id="home-box">
        <div class="report-paper" id="report-view">
            </div>
    </div>

    <div id="archive-box">
        <h2 style="margin-top:40px;">Архив департамента</h2>
        <div id="archive-list"></div>
    </div>

</div>

<div id="loginModal" style="display:none; position:fixed; top:50%; left:50%; transform:translate(-50%,-50%); background:white; padding:40px; border-top:10px solid var(--primary); z-index:1000; box-shadow: 0 0 100px rgba(0,0,0,0.5);">
    <h3 style="margin-top:0;">AUTHORIZATION</h3>
    <input type="email" id="email" placeholder="Email (.gov)" style="width:100%; padding:10px; margin-bottom:10px;">
    <input type="password" id="pass" placeholder="Password" style="width:100%; padding:10px; margin-bottom:15px;">
    <button class="nav-btn" style="background:var(--primary); width:100%;" onclick="doLogin()">ACCESS SYSTEM</button>
    <button onclick="this.parentElement.style.display='none'" style="margin-top:10px; border:none; background:none; cursor:pointer; color:gray;">Отмена</button>
</div>

<script>
    // База данных
    let reports = JSON.parse(localStorage.getItem('dol_db')) || [{
        meta: "Экземпляр №1 | Дата: 12.05.2026",
        period: "05.04.2026 — 12.05.2026",
        content: `
            <h3>1. Кадровый состав Министерства</h3>
            <table>
                <tr><td class="label">Министр Artur Drovic</td><td><span class="pct">34%</span></td></tr>
                <tr><td class="label">Заместитель Miy Li</td><td><span class="pct">33%</span></td></tr>
                <tr><td class="label">Заместитель Elmer Moroz</td><td><span class="pct">33%</span></td></tr>
            </table>

            <h3>2. Аппарат Правительства (Сан-Андреас)</h3>
            <p>Штат: 16 сотрудников</p>
            <table>
                <tr><td class="label">Выдано лицензий (Всего)</td><td><b>315</b></td></tr>
                <tr><td class="label">На оружие / Охоту / Рыбалку</td><td>239 / 33 / 33</td></tr>
                <tr><td class="label">Принято на стажировку</td><td>47 человек</td></tr>
                <tr><td class="label">Лекции / Экзамены</td><td>33 / 40</td></tr>
            </table>
            <p>Премии: Mops Sergeyevich (60%), Jamess Soprano (40%)</p>

            <h3>3. Департамент Здравоохранения</h3>
            <p>Штат: 7 сотрудников</p>
            <table>
                <tr><td class="label">Надзорные деятельности</td><td>22</td></tr>
                <tr><td class="label">Мед. проверки / Собесы EMS</td><td>4 / 6</td></tr>
                <tr><td class="label">Нарушения сотрудников EMS</td><td><b>0</b></td></tr>
                <tr><td class="label">Общая сумма штрафов</td><td><b>280.000$</b></td></tr>
                <tr><td class="label">Вознаграждения за акции</td><td>101.000$</td></tr>
            </table>
            <p>Премии: Dia Dema (50%), Dis Morales (40%), Luigi Cudi (10%)</p>
        `,
        links: "Отчет Главы Аппарата|#\nОтчет Инспектора ДЗ|#"
    }];

    function showTab(tab) {
        document.getElementById('home-box').style.display = tab === 'home' ? 'block' : 'none';
        document.getElementById('archive-box').style.display = tab === 'archive' ? 'block' : 'none';
        if(tab === 'archive') renderArchive();
    }

    function renderReport(r) {
        let linksHtml = r.links.split('\n').map(l => {
            let [name, url] = l.split('|');
            return `<a href="${url}" target="_blank">📄 ${name}</a>`;
        }).join('');

        document.getElementById('report-view').innerHTML = `
            <div class="stamp-meta">${r.meta}</div>
            <h2>Еженедельный отчет Министерства Труда</h2>
            <p style="text-align:center;">Период: <b>${r.period}</b></p>
            ${r.content}
            <h3>Прикрепленные документы</h3>
            <div class="clickable-docs">${linksHtml}</div>
        `;
    }

    function renderArchive() {
        const list = document.getElementById('archive-list');
        list.innerHTML = '';
        reports.forEach((r, idx) => {
            let div = document.createElement('div');
            div.className = 'archive-card';
            div.innerHTML = `<strong>${r.period}</strong><br><small>${r.meta}</small>`;
            div.onclick = () => { renderReport(r); showTab('home'); };
            list.appendChild(div);
        });
    }

    function openLogin() { document.getElementById('loginModal').style.display = 'block'; }

    function doLogin() {
        const e = document.getElementById('email').value;
        const p = document.getElementById('pass').value;
        if(e === 'minister@dol.gov' && p === 'Laba2026') {
            document.getElementById('editor-panel').style.display = 'block';
            document.getElementById('loginLabel').innerText = 'АДМИН-ДОСТУП';
            document.getElementById('loginModal').style.display = 'none';
            alert('С возвращением, Министр!');
        } else { alert('Access Denied!'); }
    }

    function publishReport() {
        let newR = {
            meta: document.getElementById('in-meta').value,
            period: document.getElementById('in-period').value,
            content: document.getElementById('in-content').value,
            links: document.getElementById('in-links').value
        };
        reports.unshift(newR);
        localStorage.setItem('dol_db', JSON.stringify(reports));
        location.reload();
    }

    // Старт
    renderReport(reports[0]);
</script>
</body>
</html>
