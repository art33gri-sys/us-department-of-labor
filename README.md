<!DOCTYPE html>
<html lang="uk">
<head>
    <meta charset="UTF-8">
    <title>HospitalOS v8.0 - Повна ЕМК</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        body { background-color: #eef2f7; font-family: 'Segoe UI', sans-serif; font-size: 0.85rem; }
        .navbar { background-color: #004a99; border-bottom: 4px solid #ffd700; }
        .hospital-card { border: none; border-radius: 12px; box-shadow: 0 4px 20px rgba(0,0,0,0.08); background: white; }
        .badge-narcotic { background-color: #ff004c; color: white; padding: 3px 7px; border-radius: 4px; font-weight: bold; }
        .badge-common { background-color: #00a86b; color: white; padding: 3px 7px; border-radius: 4px; }
        .scroll-area { max-height: 78vh; overflow-y: auto; border: 1px solid #d1d9e6; border-radius: 10px; background: white; }
        
        /* КАРТКА ПАЦІЄНТА (СТИЛЬ БЛАНКУ) */
        .medical-form { background: #fff; border: 1px solid #ccc; padding: 25px; box-shadow: inset 0 0 10px rgba(0,0,0,0.05); }
        .form-section { border-bottom: 1px dashed #aaa; margin-bottom: 15px; padding-bottom: 10px; }
        .form-label-custom { font-weight: bold; color: #555; text-transform: uppercase; font-size: 0.7rem; }

        /* OVERLAY */
        #dangerOverlay {
            display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            z-index: 10000; color: white; text-align: center; padding-top: 10%;
            background: rgba(150, 0, 0, 0.98);
        }
        .warning-box { border: 5px solid white; display: inline-block; padding: 40px; border-radius: 20px; animation: shake 0.5s infinite; }
        @keyframes shake { 0%, 100% {transform: translateX(0);} 25% {transform: translateX(-5px);} 75% {transform: translateX(5px);} }
    </style>
</head>
<body>

<div id="dangerOverlay">
    <div class="warning-box">
        <h1 style="font-size: 4rem;">⚠️ КРИТИЧНИЙ ДОСТУП</h1>
        <p id="alertMsg" class="fs-2 my-4"></p>
        <div class="mt-4">
            <button id="confirmBtn" class="btn btn-light btn-lg fw-bold px-5">ПІДТВЕРДИТИ ПКУ</button>
            <button onclick="closeAlert()" class="btn btn-outline-light btn-lg ms-3">СКАСУВАТИ</button>
        </div>
    </div>
</div>

<nav class="navbar navbar-dark p-2 shadow">
    <div class="container-fluid px-4">
        <span class="navbar-brand fw-bold">🏥 МІС «HospitalOS» v8.0 | Система управління стаціонаром</span>
        <span class="text-white">Черговий лікар: <span class="badge bg-warning text-dark">Адміністратор</span></span>
    </div>
</nav>

<div class="container-fluid mt-3 px-4">
    <div class="row g-3">
        <div class="col-lg-3">
            <div class="card p-3 hospital-card">
                <h6 class="fw-bold text-primary mb-3">🏥 ГОСПІТАЛІЗАЦІЯ</h6>
                <input type="text" id="ln" class="form-control form-control-sm mb-2" placeholder="Прізвище">
                <input type="text" id="fn" class="form-control form-control-sm mb-2" placeholder="Ім'я">
                <input type="text" id="wd" class="form-control form-control-sm mb-2" placeholder="Палата №">
                <textarea id="dg" class="form-control form-control-sm mb-3" placeholder="Скарги при поступленні..."></textarea>
                <button onclick="addP()" class="btn btn-primary btn-sm w-100 shadow">Створити запис</button>
            </div>
        </div>

        <div class="col-lg-9">
            <div class="card p-2 hospital-card">
                <div class="table-responsive scroll-area">
                    <table class="table table-hover align-middle mb-0">
                        <thead class="table-light">
                            <tr>
                                <th width="10%">Палата</th>
                                <th width="25%">Пацієнт (ПІБ)</th>
                                <th width="30%">Діагноз</th>
                                <th width="20%">Призначення</th>
                                <th width="15%" class="text-end">Дії</th>
                            </tr>
                        </thead>
                        <tbody id="pt"></tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
</div>

<div class="modal fade" id="chartModal" tabindex="-1">
    <div class="modal-dialog modal-xl modal-dialog-centered">
        <div class="modal-content border-0">
            <div class="modal-header bg-dark text-white">
                <h5 class="modal-title">📄 ФОРМА №003/о: <span id="cFull"></span></h5>
                <button type="button" class="btn-close btn-close-white" data-bs-dismiss="modal"></button>
            </div>
            <div class="modal-body medical-form">
                <div class="row">
                    <div class="col-md-4 border-end">
                        <div class="form-section">
                            <label class="form-label-custom">Діагноз</label>
                            <p class="fw-bold text-danger" id="cDiag"></p>
                        </div>
                        <div class="form-section">
                            <label class="form-label-custom">Активні призначення (ПКУ/Загальні)</label>
                            <div id="cMeds" class="mt-2"></div>
                            <button onclick="openAddMed()" class="btn btn-outline-success btn-sm w-100 mt-3">+ Додати препарат</button>
                        </div>
                    </div>
                    <div class="col-md-8">
                        <div class="form-section">
                            <label class="form-label-custom">Анамнез та щоденник спостереження</label>
                            <div id="cDiary" class="mt-2 bg-light p-3 rounded" style="min-height: 250px; border: 1px solid #eee;"></div>
                        </div>
                        <div class="mt-3">
                            <textarea id="cNote" class="form-control" rows="3" placeholder="Внесіть новий запис у щоденник..."></textarea>
                            <button onclick="addNote()" class="btn btn-primary mt-2">Зберегти запис</button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

<div class="modal fade" id="medModal" tabindex="-1">
    <div class="modal-dialog modal-sm modal-dialog-centered">
        <div class="modal-content">
            <div class="modal-body">
                <h6 class="fw-bold mb-3">Вибір препарату</h6>
                <select id="cat" class="form-select mb-2" onchange="fillM()">
                    <option value="">-- Каталог МНН --</option>
                    <optgroup label="Наркотичні/Психотропні">
                        <option value="Морфін|10мг|Наркотичне">Морфін (амп)</option>
                        <option value="Фентаніл|0.1мг|Наркотичне">Фентаніл (амп)</option>
                        <option value="Налбуфін|20мг|Наркотичне">Налбуфін (амп)</option>
                        <option value="Сибазон|2мл|Наркотичне">Сибазон (амп)</option>
                    </optgroup>
                    <optgroup label="Антибіотики">
                        <option value="Цефтриаксон|1.0г|Звичайне">Цефтриаксон</option>
                        <option value="Меропенем|1.0г|Звичайне">Меропенем</option>
                        <option value="Амоксиклав|1.2г|Звичайне">Амоксиклав</option>
                    </optgroup>
                    <optgroup label="Кардіологія/Реанімація">
                        <option value="Адреналін|1мл|Звичайне">Адреналін</option>
                        <option value="Аміодарон|150мг|Звичайне">Аміодарон</option>
                        <option value="Дофамін|200мг|Звичайне">Дофамін</option>
                        <option value="Фуросемід|20мг|Звичайне">Фуросемід</option>
                    </optgroup>
                </select>
                <input type="text" id="iN" class="form-control mb-1" placeholder="Назва">
                <input type="text" id="iD" class="form-control mb-1" placeholder="Дозування">
                <select id="iT" class="form-select mb-3">
                    <option value="Звичайне">Загальний облік</option>
                    <option value="Наркотичне">Суворий облік (ПКУ)</option>
                </select>
                <button onclick="checkN()" class="btn btn-primary w-100 fw-bold">ЗАТВЕРДИТИ</button>
            </div>
        </div>
    </div>
</div>

<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
<script>
    const firstNames = ["Микола", "Андрій", "Василь", "Сергій", "Ольга", "Ірина", "Дмитро", "Тетяна", "Оксана", "Віктор"];
    const lastNames = ["Коваленко", "Бондаренко", "Мельник", "Шевченко", "Ткаченко", "Поліщук", "Козак", "Олійник", "Мороз", "Кравченко"];
    const diagnoses = ["Гостра пневмонія", "Гіпертонічний криз", "Цукровий діабет, тип II", "Гострий холецистит", "Струс мозку", "Апендицит", "Ниркова коліка"];

    let patients = [];

    // ГЕНЕРУЄМО 40 УНІКАЛЬНИХ ПАЦІЄНТІВ
    for(let i=1; i<=40; i++) {
        patients.push({
            fName: firstNames[Math.floor(Math.random()*firstNames.length)],
            lName: lastNames[Math.floor(Math.random()*lastNames.length)],
            ward: 100 + i,
            diag: diagnoses[Math.floor(Math.random()*diagnoses.length)],
            meds: [],
            diary: ["Скарги на загальну слабкість.", "Потребує дообстеження."]
        });
    }

    let curIdx = null;

    function render() {
        const b = document.getElementById('pt'); b.innerHTML = "";
        patients.forEach((p, i) => {
            let mStr = p.meds.map(m => `<span class="${m.type==='Наркотичне'?'badge-narcotic':'badge-common'} me-1 mb-1 d-inline-block" style="font-size:0.65rem">${m.name}</span>`).join("");
            b.innerHTML += `<tr>
                <td><span class="badge bg-secondary p-2">Палата ${p.ward}</span></td>
                <td><b class="text-primary">${p.lName}</b> ${p.fName}</td>
                <td><small class="text-muted">${p.diag}</small></td>
                <td>${mStr || '<small class="text-muted">Порожньо</small>'}</td>
                <td class="text-end">
                    <button onclick="openChart(${i})" class="btn btn-sm btn-dark">📄 КАРТКА</button>
                    <button onclick="delP(${i})" class="btn btn-sm btn-outline-danger">✕</button>
                </td>
            </tr>`;
        });
    }

    function openChart(i) {
        curIdx = i; const p = patients[i];
        document.getElementById('cFull').innerText = p.lName.toUpperCase() + " " + p.fName;
        document.getElementById('cDiag').innerText = p.diag;
        updateChartMeds(); updateDiary();
        new bootstrap.Modal(document.getElementById('chartModal')).show();
    }

    function updateDiary() {
        const d = document.getElementById('cDiary'); d.innerHTML = "";
        patients[curIdx].diary.forEach((entry, idx) => {
            d.innerHTML += `<div class="mb-2 p-2 border-bottom bg-white" style="border-left: 3px solid #004a99">
                <small class="text-muted d-block" style="font-size:0.6rem">ЗАПИС №${idx+1}</small>
                ${entry}
            </div>`;
        });
    }

    function addNote() {
        const txt = document.getElementById('cNote').value;
        if(txt) { patients[curIdx].diary.push(txt); document.getElementById('cNote').value = ""; updateDiary(); }
    }

    function updateChartMeds() {
        const mArea = document.getElementById('cMeds'); mArea.innerHTML = "";
        patients[curIdx].meds.forEach((m, mIdx) => {
            mArea.innerHTML += `<div class="d-flex justify-content-between align-items-center mb-1 p-2 bg-light rounded">
                <span class="${m.type==='Наркотичне'?'text-danger fw-bold':''}" style="font-size:0.75rem">${m.name} [${m.dose}]</span>
                <button class="btn btn-link btn-sm text-danger text-decoration-none p-0" onclick="remMed(${mIdx})">Вилучити</button>
            </div>`;
        });
    }

    function openAddMed() { new bootstrap.Modal(document.getElementById('medModal')).show(); }

    function fillM() {
        const v = document.getElementById('cat').value;
        if(v) { const [n,d,t] = v.split('|'); document.getElementById('iN').value=n; document.getElementById('iD').value=d; document.getElementById('iT').value=t; }
    }

    function checkN() {
        const n = document.getElementById('iN').value, d = document.getElementById('iD').value, t = document.getElementById('iT').value;
        if(t === 'Наркотичне') {
            document.getElementById('alertMsg').innerText = `ВИКОРИСТАННЯ ПКУ: ${n} (${d})`;
            document.getElementById('dangerOverlay').style.display = 'block';
            document.getElementById('confirmBtn').onclick = () => { 
                saveM(n,d,t); document.getElementById('dangerOverlay').style.display = 'none'; 
            };
        } else { saveM(n,d,t); }
    }

    function saveM(n,d,t) {
        if(n && d) {
            patients[curIdx].meds.push({name:n, dose:d, type:t});
            updateChartMeds(); render();
            bootstrap.Modal.getInstance(document.getElementById('medModal')).hide();
        }
    }

    function remMed(mIdx) {
        if(confirm("Скасувати призначення даного препарату?")) {
            patients[curIdx].meds.splice(mIdx, 1);
            updateChartMeds(); render();
        }
    }

    function closeAlert() { document.getElementById('dangerOverlay').style.display = 'none'; }
    
    function addP() {
        const ln = document.getElementById('ln').value, fn = document.getElementById('fn').value;
        if(ln && fn) {
            patients.unshift({fName:fn, lName:ln, ward:document.getElementById('wd').value, diag:document.getElementById('dg').value, meds:[], diary:[]});
            render();
        }
    }

    function delP(i) { if(confirm("Виписати пацієнта зі стаціонару?")) { patients.splice(i,1); render(); } }

    render();
</script>
</body>
</html>
