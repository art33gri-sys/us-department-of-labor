<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Министерство Труда США | Официальный портал отчетности</title>
    <style>
        :root {
            --primary-blue: #002e6d;
            --secondary-blue: #205493;
            --accent-red: #cd2026;
            --light-bg: #f0f0f0;
            --white: #ffffff;
            --text-dark: #323a45;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            margin: 0;
            padding: 0;
            background-color: var(--light-bg);
            color: var(--text-dark);
            line-height: 1.6;
        }

        /* Шапка сайта */
        header {
            background-color: var(--primary-blue);
            color: var(--white);
            padding: 20px 0;
            border-bottom: 5px solid var(--accent-red);
        }

        .container {
            max-width: 1100px;
            margin: 0 auto;
            padding: 0 20px;
        }

        .header-content {
            display: flex;
            align-items: center;
            justify-content: space-between;
        }

        .logo-text h1 {
            margin: 0;
            font-size: 24px;
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        .logo-text p {
            margin: 0;
            font-size: 14px;
            opacity: 0.8;
        }

        /* Навигация */
        nav {
            background: var(--secondary-blue);
            color: white;
            padding: 10px 0;
        }

        nav a {
            color: white;
            text-decoration: none;
            margin-right: 20px;
            font-weight: bold;
            font-size: 14px;
        }

        nav a:hover {
            text-decoration: underline;
        }

        /* Основной контент */
        main {
            padding: 40px 0;
        }

        .report-card {
            background: var(--white);
            padding: 30px;
            border-radius: 4px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
            margin-bottom: 30px;
        }

        h2 {
            color: var(--primary-blue);
            border-bottom: 2px solid var(--primary-blue);
            padding-bottom: 10px;
            margin-top: 0;
        }

        h3 {
            color: var(--secondary-blue);
            background: #eef4f9;
            padding: 10px;
            border-left: 4px solid var(--secondary-blue);
        }

        .stats-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 20px;
            margin: 20px 0;
        }

        .stat-item {
            background: #f9f9f9;
            padding: 15px;
            border: 1px solid #ddd;
            text-align: center;
        }

        .stat-value {
            display: block;
            font-size: 24px;
            font-weight: bold;
            color: var(--accent-red);
        }

        /* Таблицы премирования */
        table {
            width: 100%;
            border-collapse: collapse;
            margin: 20px 0;
        }

        table th, table td {
            text-align: left;
            padding: 12px;
            border-bottom: 1px solid #ddd;
        }

        table th {
            background-color: var(--primary-blue);
            color: white;
        }

        .badge {
            background: #27ae60;
            color: white;
            padding: 4px 8px;
            border-radius: 3px;
            font-size: 12px;
        }

        /* Футер */
        footer {
            background: var(--primary-blue);
            color: white;
            text-align: center;
            padding: 30px 0;
            margin-top: 50px;
        }

        .document-link {
            display: inline-block;
            background: #e67e22;
            color: white;
            padding: 10px 20px;
            text-decoration: none;
            border-radius: 4px;
            margin-top: 10px;
        }

        .document-link:hover { background: #d35400; }
    </style>
</head>
<body>

    <header>
        <div class="container">
            <div class="header-content">
                <div class="logo-text">
                    <h1>U.S. Department of Labor</h1>
                    <p>Министерство труда Соединенных Штатов | Официальный портал</p>
                </div>
                <div class="seal">
                    <div style="width: 60px; height: 60px; background: rgba(255,255,255,0.1); border-radius: 50%; border: 2px dashed white; display: flex; align-items: center; justify-content: center; font-size: 10px; text-align: center;">DOL<br>SEAL</div>
                </div>
            </div>
        </div>
    </header>

    <nav>
        <div class="container">
            <a href="#">ГЛАВНАЯ</a>
            <a href="#">ПУБЛИЧНЫЕ ОТЧЕТЫ</a>
            <a href="#">АРХИВ ДЕПАРТАМЕНТОВ</a>
            <a href="#">ВНУТРЕННЯЯ СИСТЕМА</a>
            <a href="#">КОНТАКТЫ</a>
        </div>
    </nav>

    <main class="container">
        
        <section class="report-card">
            <h2>Еженедельный отчет Министерства Труда</h2>
            <p><strong>Период:</strong> 30.03.2026 — 05.04.2026</p>
            <p><strong>Общий состав министерства:</strong> 26 сотрудников</p>

            <h3>Руководство Министерства</h3>
            <table>
                <thead>
                    <tr>
                        <th>Должность</th>
                        <th>Имя Фамилия</th>
                        <th>Доля премии</th>
                    </tr>
                </thead>
                <tbody>
                    <tr>
                        <td>Министр Труда</td>
                        <td>Artur Drovic</td>
                        <td><span class="badge">34%</span></td>
                    </tr>
                    <tr>
                        <td>Заместитель Министра</td>
                        <td>Miy Li</td>
                        <td><span class="badge">33%</span></td>
                    </tr>
                    <tr>
                        <td>Заместитель Министра</td>
                        <td>Elmer Moroz</td>
                        <td><span class="badge">33%</span></td>
                    </tr>
                </tbody>
            </table>
        </section>

        <section class="report-card">
            <h2>Отчет Аппарата Правительства (штат Сан-Андреас)</h2>
            <p><strong>Кадровый состав:</strong> 16 сотрудников</p>
            
            <div class="stats-grid">
                <div class="stat-item">
                    <span class="stat-value">315</span>
                    Выдано лицензий
                </div>
                <div class="stat-item">
                    <span class="stat-value">239</span>
                    На оружие
                </div>
                <div class="stat-item">
                    <span class="stat-value">33</span>
                    На охоту/рыбалку
                </div>
                <div class="stat-item">
                    <span class="stat-value">40</span>
                    Экзаменов проведено
                </div>
            </div>

            <div style="background: #f4f4f4; padding: 15px; border-radius: 5px;">
                <strong>Дополнительные показатели:</strong>
                <ul>
                    <li>Собеседования в Аппарат: 6</li>
                    <li>Принято на стажировку: 24 (открытые) + 23 (закрытые)</li>
                    <li>Проведено лекций: 33</li>
                    <li>Переводов: 6</li>
                </ul>
            </div>

            <h3>Список на премирование (Аппарат)</h3>
            <ul>
                <li>Глава Аппарата <strong>Mops Sergeyevich</strong> — 60%</li>
                <li>Зам. Главы Аппарата <strong>Jamess Soprano</strong> — 40%</li>
            </ul>
        </section>

        <section class="report-card">
            <h2>Отчет Департамента Здравоохранения</h2>
            <p><strong>Кадровый состав:</strong> 7 сотрудников</p>

            <div class="stats-grid">
                <div class="stat-item">
                    <span class="stat-value">22</span>
                    Надзорные действия
                </div>
                <div class="stat-item">
                    <span class="stat-value">3</span>
                    Благотворительные акции
                </div>
                <div class="stat-item">
                    <span class="stat-value">$101,000</span>
                    Вознаграждения
                </div>
                <div class="stat-item">
                    <span class="stat-value">$280,000</span>
                    Сумма штрафов
                </div>
            </div>

            <p><strong>Активность:</strong> Присутствие на 4 мед. проверках и 6 собеседованиях в EMS. Нарушений со стороны сотрудников EMS не выявлено.</p>

            <h3>Список на премирование (Здравоохранение)</h3>
            <table>
                <tr>
                    <td>Директор Департамента</td>
                    <td>Dia Dema</td>
                    <td>50%</td>
                </tr>
                <tr>
                    <td>Зам. Директора</td>
                    <td>Dis Morales</td>
                    <td>40%</td>
                </tr>
                <tr>
                    <td>Зам. Директора</td>
                    <td>Luigi Cudi</td>
                    <td>10%</td>
                </tr>
            </table>
        </section>

        <section class="report-card" style="border-left: 5px solid #e67e22;">
            <h2>Прикрепленные документы</h2>
            <p>Официальные копии отчетов от 05.04.26:</p>
            <a href="#" class="document-link">📄 Отчет Главы Аппарата (.pdf)</a>
            <a href="#" class="document-link">📄 Отчет Инспектора Деп. Здравоохранения (.pdf)</a>
        </section>

    </main>

    <footer>
        <div class="container">
            <p>&copy; 2026 United States Department of Labor. Все права защищены.</p>
            <p style="font-size: 12px; opacity: 0.6;">Это официальный ресурс для публикации еженедельной отчетности штата Сан-Андреас.</p>
        </div>
    </footer>

</body>
</html>
