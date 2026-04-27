<!DOCTYPE html>
<html lang="uk">
<head>
    <meta charset="UTF-8">
    <title>HospitalOS v11.0 - Full Clinical Edition</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        body { background-color: #f4f6f9; font-family: 'Segoe UI', sans-serif; font-size: 0.82rem; }
        .navbar { background-color: #0056b3; color: white; box-shadow: 0 2px 8px rgba(0,0,0,0.2); }
        .sidebar-card { border: none; border-radius: 8px; box-shadow: 0 4px 12px rgba(0,0,0,0.1); background: white; }
        .badge-narcotic { background-color: #e63946; color: white; padding: 2px 6px; border-radius: 4px; font-weight: bold; animation: pulse 2s infinite; }
        @keyframes pulse { 0% { opacity: 1; } 50% { opacity: 0.6; } 100% { opacity: 1; } }
        .badge-common { background-color: #2a9d8f; color: white; padding: 2px 6px; border-radius: 4px; }
        .scroll-area { max-height: 82vh; overflow-y: auto; border-radius: 8px; border: 1px solid #ddd; }
        
        /* ЕКРАН ПКУ (НАРКОТИКИ) */
        #narcoticOverlay {
            display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            z-index: 10000; background: rgba(140, 0, 0, 0.98); color: white;
            text-align: center; padding-top: 12%; 
        }
        .warning-box { border: 8px solid white; display: inline-block; padding: 40px; border-radius: 20px; }

        /* КАРТКА ПАЦІЄНТА */
        .diag-edit { font-size: 1.1rem; font-weight: bold; color: #d90429; border: 2px solid #eee; width: 100%; padding: 8px; border-radius: 6px; }
        .diag-edit:focus { border-color: #d90429; outline: none; background: #fff8f8; }
    </style>
</head>
<body>

<div id="narcoticOverlay">
    <div class="warning-box shadow-lg">
        <h1 style="font-size: 4.5rem; font-weight: 900;">⚠️ УВАГА! ПКУ ⚠️</h1>
        <h2 id="alertMsg" class="mb-4">РЕЄСТРАЦІЯ НАРКОТИЧНОЇ РЕЧОВИНИ</h2>
        <button id="confirmBtn" class="btn btn-light btn-lg fw-bold px-5 py-3">ПІДТВЕРДИТИ ВИТРАТУ</button>
        <button onclick="closeAlert()" class="btn btn-outline-light btn-lg ms-3">СКАСУВАТИ</button>
    </div>
</div>

<nav class="navbar navbar-dark mb-3">
    <div class="container-fluid px-4">
        <span class="navbar-brand">🏥 <b>HospitalOS Pro</b> | Робоче місце лікаря | <small id="timeDisplay"></small></span>
        <span class="badge bg-light text-primary">Пацієнтів у відділенні: <b id="pCount">0</b></span>
    </div>
</nav>

<div class="container-fluid px-4">
    <div class="row">
        <div class="col-lg-3">
            <div class="card p-3 sidebar-card mb-3">
                <h6 class="text-primary fw-bold">РЕЄСТРАЦІЯ ХВОРОГО</h6>
                <hr>
                <input type="text" id="ln" class="form-control form-control-sm mb-2" placeholder="Прізвище">
                <input type="text" id="fn" class="form-control form-control-sm mb-2" placeholder="Ім'я">
                <input type="text" id="wd" class="form-control form-control-sm mb-2" placeholder="Палата №">
                <textarea id="dg" class="form-control form-control-sm mb-3" rows="3" placeholder="Попередній діагноз..."></textarea>
                <button onclick="addP()" class="btn btn-primary btn-sm w-100 fw-bold">ГОСПІТАЛІЗУВАТИ</button>
            </div>
            
            <div class="card p-2 sidebar-card bg-light">
                <small class="text-muted"><b>Довідка:</b> Натисніть на "КАРТА", щоб змінити діагноз або додати записи щоденника.</small>
            </div>
        </div>

        <div class="col-lg-9">
            <div class="card p-2 sidebar-card">
                <div class="table-responsive scroll-area">
                    <table class="table table-hover align-middle mb-0">
                        <thead class="table-dark">
                            <tr>
                                <th width="70">Палата</th>
                                <th width="200">ПІБ Пацієнта</th>
                                <th>Клінічний діагноз</th>
                                <th>Призначення</th>
                                <th class="text-end">Управління</th>
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
            <div class="modal-header bg-primary text-white">
                <h5 class="modal-title">📄 МЕДИЧНА КАРТА СТАЦІОНАРНОГО ХВОРОГО: <span id="cFull"></span></h5>
                <button type="button" class="btn-close btn-close-white" data-bs-dismiss="modal"></button>
            </div>
            <div class="modal-body p-4 bg-white">
                <div class="row">
                    <div class="col-md-5 border-end">
                        <label class="fw-bold text-muted small">КЛІНІЧНИЙ ДІАГНОЗ (РЕДАГУВАННЯ):</label>
                        <textarea id="cDiag" class="diag-edit mb-3" rows="3" onchange="updateDiag()"></textarea>
                        
                        <label class="fw-bold text-muted small">ЛИСТОК ПРИЗНАЧЕНЬ:</label>
                        <div id="cMeds" class="bg-light p-3 rounded border mb-2" style="min-height: 180px;"></div>
                        <button onclick="openAddMed()" class="btn btn-success btn-sm w-100 fw-bold">+ ПРИЗНАЧИТИ ПРЕПАРАТ</button>
                    </div>
                    <div class="col-md-7">
                        <label class="fw-bold text-muted small">ЩОДЕННИК ОБХОДУ:</label>
                        <div id="cDiary" class="bg-light p-3 mb-2 border rounded" style="height: 250px; overflow-y: auto;"></div>
                        <div class="input-group">
                            <textarea id="cNote" class="form-control" rows="2" placeholder="Введіть стан пацієнта..."></textarea>
                            <button onclick="addNote()" class="btn btn-primary px-4">ДОДАТИ</button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

<div class="modal fade" id="medModal" tabindex="-1">
    <div class="modal-dialog modal-sm modal-dialog-centered">
        <div class="modal-content shadow-lg border-0">
            <div class="modal-body p-4 text-center">
                <h6 class="fw-bold mb-3">ОБЕРІТЬ ПРЕПАРАТ</h6>
                <select id="cat" class="form-select mb-2" onchange="fillM()">
                    <option value="">-- Каталог МНН --</option>
                    <optgroup label="НАРКОТИКИ / ПКУ ⚠️">
                        <option value="Морфін|10 мг|narc">Морфін (амп)</option>
                        <option value="Фентаніл|0.1 мг|narc">Фентаніл (амп)</option>
                        <option value="Промедол|20 мг|narc">Промедол (амп)</option>
                        <option value="Сибазон|10 мг|narc">Сибазон (амп)</option>
                        <option value="Трамадол|100 мг|narc">Трамадол (амп)</option>
                    </optgroup>
                    <optgroup label="АНТИБІОТИКИ">
                        <option value="Цефтриаксон|1.0 г|common">Цефтриаксон</option>
                        <option value="Меропенем|1.0 г|common">Меропенем</option>
                        <option value="Ванкоміцин|500 мг|common">Ванкоміцин</option>
                        <option value="Метронідазол|100 мл|common">Метронідазол</option>
                    </optgroup>
                    <optgroup label="РЕАНІМАЦІЯ / СЕРЦЕВІ">
                        <option value="Адреналін|1 мл|common">Адреналін</option>
                        <option value="Дофамін|200 мг|common">Дофамін</option>
                        <option value="Аміодарон|150 мг|common">Аміодарон</option>
                        <option value="Гепарин|5000 ОД|common">Гепарин</option>
                        <option value="Фуросемід|20 мг|common">Фуросемід</option>
                    </optgroup>
                    <optgroup label="ПРОЧЕ">
                        <option value="Дексаметазон|4 мг|common">Дексаметазон</option>
                        <option value="Анальгін|2.0|common">Анальгін</option>
                        <option value="Димедрол|1.0|common">Димедрол</option>
                        <option value="Кеторолак|1.0|common">Кеторолак</option>
                    </optgroup>
                </select>
                <input type="text" id="iN" class="form-control mb-1" placeholder="Назва">
                <input type="text" id="iD" class="form-control mb-3" placeholder="Доза">
                <button onclick="processMed()" class="btn btn-primary w-100 fw-bold">ЗАТВЕРДИТИ</button>
            </div>
        </div>
    </div>
</div>

<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
<script>
    const narcTrigger = ["Морфін", "Фентаніл", "Промедол", "Сибазон", "Трамадол", "Налбуфін"];
    const surnames = ["Бондаренко", "Мельник", "Шевченко", "Ткаченко", "Коваленко", "Олійник", "Мороз", "Козак", "Павленко", "Кравченко"];
    const names = ["Олександр", "Дмитро", "Сергій", "Андрій", "Іван", "Марія", "Олена", "Оксана", "Тетяна", "Василь"];
    const clinicalDiagnoses = [
        "Гострий панкреатит, набрякова форма",
        "Шлунково-кишкова кровотеча, активна",
        "Гостра двобічна пневмонія, тягаий перебіг",
        "Закрита черепно-мозкова травма, струс мозку",
        "Інфаркт міокарда передньої стінки лівого шлуночка",
        "Цукровий діабет II типу, стан декомпенсації",
        "Ниркова коліка справа, сечокам'яна хвороба",
        "Гострий апендицит, деструктивна форма",
        "Хронічна серцева недостатність IIБ ст.",
        "Гостре порушення мозкового кровообігу (Інсульт)"
    ];

    let patients = [];
    // Генерація 35 пацієнтів з реальними діагнозами
    for(let i=1; i<=35; i++){
        patients.push({
            fName: names[Math.floor(Math.random()*names.length)],
            lName: surnames[Math.floor(Math.random()*surnames.length)],
            ward: 100 + i,
            diag: clinicalDiagnoses[i % clinicalDiagnoses.length],
            meds: [], diary: ["Хворий госпіталізований за екстреними показаннями."]
        });
    }

    let curIdx = null;

    function render() {
        const b = document.getElementById('pt'); b.innerHTML = "";
        document.getElementById('pCount').innerText = patients.length;
        patients.forEach((p, i) => {
            let mStr = p.meds.map(m => `<span class="${m.isN?'badge-narcotic':'badge-common'} me-1 mb-1 d-inline-block">${m.name}</span>`).join("");
            b.innerHTML += `<tr>
                <td><span class="badge bg-primary p-2">№${p.ward}</span></td>
                <td><b>${p.lName}</b> ${p.fName}</td>
                <td><small class="text-danger fw-bold">${p.diag}</small></td>
                <td>${mStr || '<i class="text-muted">немає</i>'}</td>
                <td class="text-end">
                    <button onclick="openChart(${i})" class="btn btn-sm btn-dark">📄 КАРТА</button>
                    <button onclick="delP(${i})" class="btn btn-sm btn-outline-danger">✕</button>
                </td>
            </tr>`;
        });
    }

    function openChart(i) {
        curIdx = i; const p = patients[i];
        document.getElementById('cFull').innerText = p.lName + " " + p.fName;
        document.getElementById('cDiag').value = p.diag;
        updateChartMeds(); updateDiary();
        new bootstrap.Modal(document.getElementById('chartModal')).show();
    }

    function updateDiag() { patients[curIdx].diag = document.getElementById('cDiag').value; render(); }
    function updateDiary() { 
        const d = document.getElementById('cDiary'); d.innerHTML = "";
        patients[curIdx].diary.forEach(e => d.innerHTML += `<div class="p-2 border-bottom bg-white mb-1 rounded small shadow-sm">${e}</div>`);
    }
    function addNote() { const t = document.getElementById('cNote').value; if(t) { patients[curIdx].diary.push(t); document.getElementById('cNote').value = ""; updateDiary(); } }

    function updateChartMeds() {
        const a = document.getElementById('cMeds'); a.innerHTML = "";
        patients[curIdx].meds.forEach((m, idx) => {
            a.innerHTML += `<div class="d-flex justify-content-between p-2 mb-1 bg-white border rounded shadow-sm">
                <span class="${m.isN?'text-danger fw-bold':''}">${m.name} - ${m.dose}</span>
                <button class="btn btn-link text-danger btn-sm p-0" onclick="remM(${idx})">вилучити</button>
            </div>`;
        });
    }

    function openAddMed() { new bootstrap.Modal(document.getElementById('medModal')).show(); }
    function fillM() { const v = document.getElementById('cat').value; if(v) { const [n,d] = v.split('|'); document.getElementById('iN').value=n; document.getElementById('iD').value=d; } }

    function processMed() {
        const n = document.getElementById('iN').value, d = document.getElementById('iD').value;
        const isN = narcTrigger.some(nn => n.toLowerCase().includes(nn.toLowerCase()));
        if(isN) {
            document.getElementById('alertMsg').innerText = `РЕЄСТРАЦІЯ ВИДАЧІ ПКУ: ${n.toUpperCase()}`;
            document.getElementById('narcoticOverlay').style.display = 'block';
            document.getElementById('confirmBtn').onclick = () => { saveM(n,d,true); closeAlert(); };
        } else { saveM(n,d,false); }
    }

    function saveM(n,d,isN) {
        if(n && d) {
            patients[curIdx].meds.push({name:n, dose:d, isN:isN});
            updateChartMeds(); render();
            bootstrap.Modal.getInstance(document.getElementById('medModal')).hide();
        }
    }

    function remM(idx) {
        const isN = patients[curIdx].meds[idx].isN;
        if(confirm(isN ? "⚠️ УВАГА! ВИ ВИДАЛЯЄТЕ НАРКОТИЧНИЙ ПРЕПАРАТ З ЛИСТКА ПРИЗНАЧЕНЬ!" : "Вилучити препарат?")) {
            patients[curIdx].meds.splice(idx,1); updateChartMeds(); render();
        }
    }

    function closeAlert() { document.getElementById('narcoticOverlay').style.display = 'none'; }
    function addP() { 
        const ln = document.getElementById('ln').value, fn = document.getElementById('fn').value; 
        if(ln && fn) { 
            patients.unshift({fName:fn, lName:ln, ward:document.getElementById('wd').value, diag:document.getElementById('dg').value, meds:[], diary:[]}); 
            render(); 
        } 
    }
    function delP(i) { if(confirm("Виписати хворого зі стаціонару?")) { patients.splice(i,1); render(); } }

    setInterval(() => { document.getElementById('timeDisplay').innerText = new Date().toLocaleString('uk-UA'); }, 1000);
    render();
</script>
</body>
</html>
