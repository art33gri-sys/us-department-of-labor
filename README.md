<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>U.S. Department of Labor | Системный Портал</title>
    
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>

    <style>
        :root {
            --gov-blue: #002e6d;
            --gov-navy: #112e51;
            --gov-red: #cd2026;
            --gov-light: #f0f3f5;
            --gold: #b69156;
        }

        body { font-family: 'Source Sans Pro', sans-serif; margin: 0; background: var(--gov-light); color: #333; }
        
        /* UI Элементы */
        .top-bar { background: var(--gov-navy); color: white; padding: 10px 0; border-bottom: 3px solid var(--gold); }
        .header { background: white; padding: 20px 0; box-shadow: 0 4px 10px rgba(0,0,0,0.1); }
        .container { max-width: 1200px; margin: 0 auto; padding: 0 20px; }
        
        .flex { display: flex; align-items: center; justify-content: space-between; }
        .logo-section { display: flex; align-items: center; gap: 20px; }
        .logo-section img { width: 90px; }
        
        .nav-link { text-decoration: none; color: var(--gov-blue); font-weight: bold; margin-right: 20px; text-transform: uppercase; font-size: 13px; }
        
        /* Карточки и Отчеты */
        .report-card { background: white; border: 1px solid #ccc; padding: 30px; margin-bottom: 25px; position: relative; border-radius: 4px; }
        .report-card::before { content: ""; position: absolute; top: 0; left: 0; width: 5px; height: 100%; background: var(--gov-blue); }
        
        .stamp { position: absolute; top: 20px; right: 20px; border: 3px double var(--gov-red); color: var(--gov-red); padding: 10px; transform: rotate(15deg); font-weight: bold; opacity: 0.7; text-transform: uppercase; }
        
        .grid { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; }
        .data-block { background: #f9f9f9; padding: 15px; border: 1px solid #ddd; }
        
        /* Кнопки и формы */
        .btn { padding: 10px 20px; border: none; cursor: pointer; font-weight: bold; border-radius: 2px; }
        .btn-primary { background: var(--gov-blue); color: white; }
        .btn-admin { background: var(--gov-red); color: white; }
        
        .hidden { display: none; }
        
        input, textarea, select { width: 100%; padding: 10px; margin: 10px 0; border: 1px solid #ccc; box-sizing: border-box; }
        
        .status-tag { padding: 5px 10px; border-radius: 20px; font-size: 12px; background: #e0e0e0; }
    </style>
</head>
<body>

<div class="top-bar">
    <div class="container flex">
        <div>An official website of the United States government</div>
        <div id="auth-status">Вход не выполнен</div>
    </div>
</div>

<div class="header">
    <div class="container flex">
        <div class="logo-section">
            <img src="https://i.ibb.co/VWVm0Qz/dol-logo.png" alt="Logo">
            <div>
                <h1 style="margin:0; font-size: 24px; color: var(--gov-navy);">UNITED STATES DEPARTMENT OF LABOR</h1>
                <div style="color: var(--gov-red); font-weight: bold; letter-spacing: 2px;">SECRETARY OF LABOR | ARCHIVE SYSTEM</div>
            </div>
        </div>
        <div id="nav-buttons">
            <button class="btn btn-primary" onclick="showPage('public')">Публичные отчеты</button>
            <button class="btn btn-admin" id="login-btn" onclick="showPage('login')">Вход для сотрудников</button>
            <button class="btn btn-admin hidden" id="add-report-btn" onclick="showPage('editor')">+ Подать отчет</button>
        </div>
    </div>
</div>

<div class="container" style="margin-top: 30px;">
    
    <div id="page-public">
        <h2 style="border-bottom: 2px solid var(--gov-blue); padding-bottom: 10px;">Реестр еженедельной отчетности</h2>
        <div id="reports-list">
            <div class="report-card">
                <div class="stamp">Одобрено</div>
                <h3>Еженедельный отчет: Artur Drovic</h3>
                <p>Период: 05.04.2026 — 12.05.2026</p>
                <hr>
                <div class="grid">
                    <div class="data-block">
                        <strong>📊 Аппарат Правительства:</strong><br>
                        Лицензии: 315 (Оружие: 239)<br>
                        Собеседования: 6<br>
                        Стажировки: 47 человек
                    </div>
                    <div class="data-block">
                        <strong>⚕️ Деп. Здравоохранения:</strong><br>
                        Надзор: 22 | Штрафы: 280.000$<br>
                        Благотворительность: 101.000$
                    </div>
                </div>
                <div style="margin-top: 15px;">
                    <strong>Премиальный фонд:</strong> Artur Drovic (34%), Miy Li (33%), Elmer Moroz (33%)
                </div>
            </div>
        </div>
    </div>

    <div id="page-login" class="hidden" style="max-width: 400px; margin: 0 auto; background: white; padding: 40px; box-shadow: 0 0 20px rgba(0,0,0,0.1);">
        <h3>Авторизация сотрудника</h3>
        <input type="email" id="email" placeholder="Email (gov.us)">
        <input type="password" id="password" placeholder="Пароль">
        <button class="btn btn-primary" style="width: 100%;" onclick="handleLogin()">Войти в систему</button>
    </div>

    <div id="page-editor" class="hidden">
        <h2>Форма подачи государственного отчета</h2>
        <div class="report-card">
            <label>Имя подающего (Министр/Зам):</label>
            <input type="text" id="form-name" value="Artur Drovic">
            
            <label>Период отчета:</label>
            <input type="text" id="form-period" value="05.04.2026 - 12.05.2026">

            <label>Текст отчета (всё содержимое):</label>
            <textarea id="form-content" rows="15" style="font-family: monospace;"></textarea>
            
            <button class="btn btn-primary" onclick="submitReport()">ОПУБЛИКОВАТЬ ОТЧЕТ В БАЗУ</button>
        </div>
    </div>
</div>

<script>
    // --- КОНФИГУРАЦИЯ FIREBASE ---
    const firebaseConfig = {
        apiKey: "ТВОЙ_API_KEY",
        authDomain: "ТВОЙ_PROJECT.firebaseapp.com",
        projectId: "ТВОЙ_PROJECT",
        storageBucket: "ТВОЙ_PROJECT.appspot.com",
        messagingSenderId: "ID",
        appId: "APP_ID"
    };

    // Инициализация
    firebase.initializeApp(firebaseConfig);
    const auth = firebase.auth();
    const db = firebase.firestore();

    // Переключение страниц
    function showPage(pageId) {
        document.querySelectorAll('[id^="page-"]').forEach(p => p.classList.add('hidden'));
        document.getElementById('page-' + pageId).classList.remove('hidden');
    }

    // ЛОГИКА АВТОРИЗАЦИИ
    async function handleLogin() {
        const e = document.getElementById('email').value;
        const p = document.getElementById('password').value;
        try {
            await auth.signInWithEmailAndPassword(e, p);
            alert("Вход выполнен!");
            showPage('public');
        } catch (err) { alert("Ошибка: " + err.message); }
    }

    auth.onAuthStateChanged(user => {
        if (user) {
            document.getElementById('auth-status').innerText = "Система: " + user.email;
            document.getElementById('login-btn').classList.add('hidden');
            document.getElementById('add-report-btn').classList.remove('hidden');
        } else {
            document.getElementById('auth-status').innerText = "Вход не выполнен";
        }
    });

    // РАБОТА С ОТЧЕТАМИ (FIRESTORE)
    function submitReport() {
        const report = {
            author: document.getElementById('form-name').value,
            period: document.getElementById('form-period').value,
            content: document.getElementById('form-content').value,
            timestamp: firebase.firestore.FieldValue.serverTimestamp(),
            status: "Опубликовано"
        };

        db.collection("reports").add(report).then(() => {
            alert("Отчет успешно внесен в реестр!");
            showPage('public');
        });
    }

    // REAL-TIME ОБНОВЛЕНИЕ СПИСКА
    db.collection("reports").orderBy("timestamp", "desc").onSnapshot(snapshot => {
        const list = document.getElementById('reports-list');
        list.innerHTML = ''; // Очистка
        snapshot.forEach(doc => {
            const data = doc.data();
            list.innerHTML += `
                <div class="report-card">
                    <div class="stamp">${data.status}</div>
                    <h3>Еженедельный отчет: ${data.author}</h3>
                    <p><b>Период:</b> ${data.period}</p>
                    <div style="white-space: pre-line; background: #f4f4f4; padding: 15px; border-radius: 5px;">
                        ${data.content}
                    </div>
                    <div style="margin-top:10px; font-size: 10px; color: gray;">
                        ID Документа: ${doc.id}
                    </div>
                </div>
            `;
        });
    });
</script>

</body>
</html>
