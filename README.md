<!DOCTYPE html>
<html lang="uk">
<head>
    <meta charset="UTF-8">
    <title>HospitalOS v10.0 - Classic Edition</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        body { background-color: #f0f2f5; font-family: 'Segoe UI', sans-serif; font-size: 0.85rem; }
        .navbar { background-color: #0d6efd; color: white; box-shadow: 0 2px 10px rgba(0,0,0,0.1); }
        .hospital-card { border: none; border-radius: 12px; box-shadow: 0 4px 20px rgba(0,0,0,0.1); background: white; }
        .badge-narcotic { background-color: #dc3545; color: white; padding: 2px 6px; border-radius: 4px; font-weight: bold; animation: pulse 1.5s infinite; }
        @keyframes pulse { 0% { opacity: 1; } 50% { opacity: 0.7; } 100% { opacity: 1; } }
        .badge-common { background-color: #198754; color: white; padding: 2px 6px; border-radius: 4px; }
        .scroll-area { max-height: 80vh; overflow-y: auto; }
        
        /* ЕКРАН ТРИВОГИ */
        #narcoticOverlay {
            display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            z-index: 10000; background: rgba(150, 0, 0, 0.98); color: white;
            text-align: center; padding-top: 10%; font-family: 'Impact', sans-serif;
        }
        .warning-box { border: 10px solid white; display: inline-block; padding: 50px; border-radius: 30px; }
        
        /* КАРТКА */
        .diag-edit { font-size: 1.3rem; font-weight: bold; color: #dc3545; border: 1px solid #ddd; width: 100%; padding: 5px; border-radius: 5px; }
    </style>
</head>
<body>

<div id="narcoticOverlay">
    <div class="warning-box">
        <h1 style="font-size: 5rem;">⚠️ СУВОРИЙ ОБЛІК ⚠️</h1>
        <h2 id="alertMsg" class="mb-4">ВИПИСКА НАРКОТИЧНОГО ЗАСОБУ</h2>
        <button id="confirmBtn" class="btn btn-light btn-lg fw-bold px-5 py-3">ПІДТВЕРДИТИ ВИТРАТУ</button>
        <button onclick="closeAlert()" class="btn btn-outline-light btn-lg ms-3">СКАСУВАТИ</button>
    </div>
</div>

<nav class="navbar navbar-dark mb-3">
    <div class="container-fluid px-4">
        <span class="navbar-brand">🏥 <b>HospitalOS v10.0</b> | Пост медичної сестри</span>
        <span class="text-white small">Активних пацієнтів: <b id="pCount">0</b></span>
    </div>
</nav>

<div class="container-fluid px-4">
    <div class="row">
        <div class="col-lg-3">
            <div class="card p-3 hospital-card mb-3">
                <h6 class="text-primary fw-bold">ПРИЙОМНИЙ ВІДДІЛ</h6>
                <hr>
                <input type="text" id="ln" class="form-control form-control-sm mb-2" placeholder="Прізвище">
                <input type="text" id="fn" class="form-control form-control-sm mb-2" placeholder="Ім'я">
                <input type="text" id="wd" class="form-control form-control-sm mb-2" placeholder="Палата">
                <textarea id="dg" class="form-control form-control-sm mb-3" placeholder="Діагноз..."></textarea>
                <button onclick="addP()" class="btn btn-primary btn-sm w-100 fw-bold">ГОСПІТАЛІЗУВАТИ</button>
            </div>
        </div>

        <div class="col-lg-9">
            <div class="card p-2 hospital-card">
                <div class="table-responsive scroll-area">
                    <table class="table table-hover align-middle">
                        <thead class="table-dark">
                            <tr>
                                <th width="80">Палата</th>
                                <th>Пацієнт</th>
                                <th>Клінічний діагноз</th>
                                <th>Ліки</th>
                                <th class="text-end">Дія</th>
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
        <div class="modal-content">
            <div class="modal-header bg-primary text-white">
                <h5 class="modal-title">📄 КАРТА ПАЦІЄНТА: <span id="cFull"></span></h5>
                <button type="button" class="btn-close btn-close-white" data-bs-dismiss="modal"></button>
            </div>
            <div class="modal-body p-4">
                <div class="row">
                    <div class="col-md-5 border-end">
                        <label class="fw-bold text-muted small">РЕДАГУВАТИ ДІАГНОЗ:</label>
                        <input type="text" id="cDiag" class="diag-edit mb-3" onchange="updateDiag()">
                        
                        <label class="fw-bold text-muted small">ПРИЗНАЧЕННЯ:</label>
                        <div id="cMeds" class="bg-light p-2 rounded border" style="min-height: 150px;"></div>
                        <button onclick="openAddMed()" class="btn btn-success btn-sm w-100 mt-2">+ ДОДАТИ ЛІКИ</button>
                    </div>
                    <div class="col-md-7">
                        <label class="fw-bold text-muted small">ЩОДЕННИК ЛІКАРЯ:</label>
                        <div id="cDiary" class="bg-light p-2 mb-2 border rounded" style="height: 200px; overflow-y: auto;"></div>
                        <textarea id="cNote" class="form-control mb-2" rows="2" placeholder="Новий запис..."></textarea>
                        <button onclick="addNote()" class="btn btn-primary btn-sm w-100">ЗБЕРЕГТИ ЗАПИС</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

<div class="modal fade" id="medModal" tabindex="-1">
    <div class="modal-dialog modal-sm modal-dialog-centered">
        <div class="modal-content border-0 shadow">
            <div class="modal-body p-4">
                <h6 class="fw-bold text-center">КАТАЛОГ ЛІКІВ</h6>
                <select id="cat" class="form-select mb-2" onchange="fillM()">
                    <option value="">-- Оберіть препарат --</option>
                    <optgroup label="НАРКОТИКИ (ПКУ) ⚠️">
                        <option value="Морфін|1% 1.0|isNarc">Морфін</option>
                        <option value="Фентаніл|0.005% 2.0|isNarc">Фентаніл</option>
                        <option value="Промедол|2% 1.0|isNarc">Промедол</option>
                        <option value="Трамадол|5% 2.0|isNarc">Трамадол</option>
                        <option value="Сибазон|0.5% 2.0|isNarc">Сибазон</option>
                    </optgroup>
                    <optgroup label="АНТИБІОТИКИ">
                        <option value="Цефтриаксон|1.0г|common">Цефтриаксон</option>
                        <option value="Амоксиклав|1.2г|common">Амоксиклав</option>
                        <option value="Левофлоксацин|500мг|common">Левофлоксацин</option>
                    </optgroup>
                    <optgroup label="ІНШЕ">
                        <option value="Анальгін|2.0|common">Анальгін</option>
                        <option value="Димедрол|1% 1.0|common">Димедрол</option>
                        <option value="Дексаметазон|4мг|common">Дексаметазон</option>
                        <option value="Фуросемід|2.0|common">Фуросемід</option>
                        <option value="Гепарин|5000 ОД|common">Гепарин</option>
                        <option value="Аміодарон|150мг|common">Аміодарон</option>
                    </optgroup>
                </select>
                <input type="text" id="iN" class="form-control mb-1" placeholder="Назва ліків">
                <input type="text" id="iD" class="form-control mb-3" placeholder="Доза">
                <button onclick="processMed()" class="btn btn-primary w-100 fw-bold">ЗАТВЕРДИТИ</button>
            </div>
        </div>
    </div>
</div>

<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
<script>
    const narcNames = ["Морфін", "Фентаніл", "Промедол", "Трамадол", "Сибазон", "Налбуфін"];
    const surnames = ["Кравченко", "Шевчук", "Коваленко", "Мельник", "Бондар", "Ткач", "Лисенко", "Мороз", "Козак", "Олійник"];
    const names = ["Олександр", "Дмитро", "Іван", "Марія", "Олена", "Андрій", "Сергій", "Тетяна", "Василь", "Анна"];

    let patients = [];
    for(let i=1; i<=35; i++){
        patients.push({
            fName: names[Math.floor(Math.random()*10)],
            lName: surnames[Math.floor(Math.random()*10)],
            ward: 100 + i,
            diag: "Діагноз пацієнта №" + i,
            meds: [], diary: ["Пацієнт поступив."]
        });
    }

    let curIdx = null;

    function render() {
        const b = document.getElementById('pt'); b.innerHTML = "";
        document.getElementById('pCount').innerText = patients.length;
        patients.forEach((p, i) => {
            let mStr = p.meds.map(m => `<span class="${m.isN?'badge-narcotic':'badge-common'} me-1 mb-1 d-inline-block">${m.name}</span>`).join("");
            b.innerHTML += `<tr>
                <td><span class="badge bg-primary">№${p.ward}</span></td>
                <td><b>${p.lName}</b> ${p.fName}</td>
                <td><small class="text-danger fw-bold">${p.diag}</small></td>
                <td>${mStr}</td>
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
        patients[curIdx].diary.forEach(e => d.innerHTML += `<div class="p-1 border-bottom small">${e}</div>`);
    }
    function addNote() { const t = document.getElementById('cNote').value; if(t) { patients[curIdx].diary.push(t); document.getElementById('cNote').value = ""; updateDiary(); } }

    function updateChartMeds() {
        const a = document.getElementById('cMeds'); a.innerHTML = "";
        patients[curIdx].meds.forEach((m, idx) => {
            a.innerHTML += `<div class="d-flex justify-content-between p-1 border-bottom">
                <span class="${m.isN?'text-danger fw-bold':''}">${m.name} (${m.dose})</span>
                <button class="btn btn-link text-danger btn-sm p-0" onclick="remM(${idx})">видалити</button>
            </div>`;
        });
    }

    function openAddMed() { new bootstrap.Modal(document.getElementById('medModal')).show(); }
    function fillM() { const v = document.getElementById('cat').value; if(v) { const [n,d] = v.split('|'); document.getElementById('iN').value=n; document.getElementById('iD').value=d; } }

    function processMed() {
        const n = document.getElementById('iN').value, d = document.getElementById('iD').value;
        const isN = narcNames.some(nn => n.toLowerCase().includes(nn.toLowerCase()));
        if(isN) {
            document.getElementById('alertMsg').innerText = `ПКУ ПРЕПАРАТ: ${n.toUpperCase()}`;
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
        if(confirm(isN ? "⚠️ ВИДАЛЕННЯ НАРКОТИКА!" : "Видалити?")) { patients[curIdx].meds.splice(idx,1); updateChartMeds(); render(); }
    }

    function closeAlert() { document.getElementById('narcoticOverlay').style.display = 'none'; }
    function addP() { const ln = document.getElementById('ln').value, fn = document.getElementById('fn').value; if(ln && fn) { patients.unshift({fName:fn, lName:ln, ward:document.getElementById('wd').value, diag:document.getElementById('dg').value, meds:[], diary:[]}); render(); } }
    function delP(i) { if(confirm("Виписати?")) { patients.splice(i,1); render(); } }

    render();
</script>
</body>
</html>
