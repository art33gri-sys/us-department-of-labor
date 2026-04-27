<!DOCTYPE html>
<html lang="uk">
<head>
    <meta charset="UTF-8">
    <title>HospitalOS v12.0 - Fixed Database</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        body { background-color: #f4f6f9; font-family: 'Segoe UI', sans-serif; font-size: 0.82rem; }
        .navbar { background-color: #0056b3; color: white; box-shadow: 0 2px 8px rgba(0,0,0,0.2); }
        .sidebar-card { border: none; border-radius: 8px; box-shadow: 0 4px 12px rgba(0,0,0,0.1); background: white; }
        .badge-narcotic { background-color: #e63946; color: white; padding: 2px 6px; border-radius: 4px; font-weight: bold; animation: pulse 2s infinite; }
        @keyframes pulse { 0% { opacity: 1; } 50% { opacity: 0.6; } 100% { opacity: 1; } }
        .badge-common { background-color: #2a9d8f; color: white; padding: 2px 6px; border-radius: 4px; }
        .scroll-area { max-height: 82vh; overflow-y: auto; border-radius: 8px; border: 1px solid #ddd; }
        
        #narcoticOverlay {
            display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            z-index: 10000; background: rgba(140, 0, 0, 0.98); color: white;
            text-align: center; padding-top: 12%; 
        }
        .warning-box { border: 8px solid white; display: inline-block; padding: 40px; border-radius: 20px; }
        .diag-edit { font-size: 1.1rem; font-weight: bold; color: #d90429; border: 2px solid #eee; width: 100%; padding: 8px; border-radius: 6px; }
    </style>
</head>
<body>

<div id="narcoticOverlay">
    <div class="warning-box shadow-lg">
        <h1 style="font-size: 4.5rem; font-weight: 900;">⚠️ ПКУ ⚠️</h1>
        <h2 id="alertMsg" class="mb-4">КОНТРОЛЬ НАРКОТИКІВ</h2>
        <button id="confirmBtn" class="btn btn-light btn-lg fw-bold px-5 py-3">ПІДТВЕРДИТИ</button>
        <button onclick="closeAlert()" class="btn btn-outline-light btn-lg ms-3">СКАСУВАТИ</button>
    </div>
</div>

<nav class="navbar navbar-dark mb-3">
    <div class="container-fluid px-4">
        <span class="navbar-brand">🏥 <b>HospitalOS Pro</b> | Система Стаціонару</span>
        <span class="badge bg-light text-primary">Пацієнтів: <b id="pCount">0</b></span>
    </div>
</nav>

<div class="container-fluid px-4">
    <div class="row">
        <div class="col-lg-3">
            <div class="card p-3 sidebar-card mb-3">
                <h6 class="text-primary fw-bold">НОВИЙ ПАЦІЄНТ</h6>
                <hr>
                <input type="text" id="ln" class="form-control form-control-sm mb-2" placeholder="Прізвище">
                <input type="text" id="fn" class="form-control form-control-sm mb-2" placeholder="Ім'я">
                <input type="text" id="wd" class="form-control form-control-sm mb-2" placeholder="Палата">
                <textarea id="dg" class="form-control form-control-sm mb-3" rows="2" placeholder="Діагноз"></textarea>
                <button onclick="addP()" class="btn btn-primary btn-sm w-100 fw-bold">ДОДАТИ</button>
            </div>
        </div>

        <div class="col-lg-9">
            <div class="card p-2 sidebar-card">
                <div class="table-responsive scroll-area">
                    <table class="table table-hover align-middle mb-0">
                        <thead class="table-dark">
                            <tr>
                                <th width="70">Палата</th>
                                <th width="220">Пацієнт</th>
                                <th>Діагноз</th>
                                <th>Призначення</th>
                                <th class="text-end">Дії</th>
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
                <h5 class="modal-title">📄 Електронна карта: <span id="cFull"></span></h5>
                <button type="button" class="btn-close btn-close-white" data-bs-dismiss="modal"></button>
            </div>
            <div class="modal-body p-4">
                <div class="row">
                    <div class="col-md-5 border-end">
                        <label class="fw-bold small">ДІАГНОЗ (РЕДАГУВАННЯ):</label>
                        <textarea id="cDiag" class="diag-edit mb-3" rows="2" onchange="updateDiag()"></textarea>
                        <label class="fw-bold small">ЛІКИ ТА МАНІПУЛЯЦІЇ:</label>
                        <div id="cMeds" class="bg-light p-2 rounded border mb-2" style="min-height: 150px;"></div>
                        <button onclick="openAddMed()" class="btn btn-success btn-sm w-100">+ ДОДАТИ</button>
                    </div>
                    <div class="col-md-7">
                        <label class="fw-bold small">ЖУРНАЛ:</label>
                        <div id="cDiary" class="bg-light p-2 mb-2 border rounded" style="height: 200px; overflow-y: auto;"></div>
                        <textarea id="cNote" class="form-control mb-1" rows="2" placeholder="Запис..."></textarea>
                        <button onclick="addNote()" class="btn btn-primary btn-sm w-100">ЗБЕРЕГТИ</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

<div class="modal fade" id="medModal" tabindex="-1">
    <div class="modal-dialog modal-sm modal-dialog-centered">
        <div class="modal-content shadow-lg border-0">
            <div class="modal-body p-4">
                <select id="cat" class="form-select mb-2" onchange="fillM()">
                    <option value="">-- Виберіть --</option>
                    <option value="Морфін|10 мг|narc">Морфін ⚠️</option>
                    <option value="Фентаніл|0.1 мг|narc">Фентаніл ⚠️</option>
                    <option value="Операція|Апендектомія|common">Операція</option>
                    <option value="Клізма|Очисна|common">Клізма</option>
                    <option value="Анальгін|2.0|common">Анальгін</option>
                </select>
                <input type="text" id="iN" class="form-control mb-1" placeholder="Назва">
                <input type="text" id="iD" class="form-control mb-3" placeholder="Доза">
                <button onclick="processMed()" class="btn btn-primary w-100">ПІДТВЕРДИТИ</button>
            </div>
        </div>
    </div>
</div>

<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
<script>
    // ФІКСОВАНА БАЗА (35 РІЗНИХ ЛЮДЕЙ)
    let patients = [
        {fName:"Іван", lName:"Бондар", ward:101, diag:"Гострий апендицит", meds:[{name:"Операція", dose:"ургентна", isN:false}, {name:"Морфін", dose:"10мг", isN:true}], diary:["Готується до операції."]},
        {fName:"Марія", lName:"Козак", ward:102, diag:"Панкреатит", meds:[{name:"Клізма", dose:"очисна", isN:false}, {name:"Анальгін", dose:"2мл", isN:false}], diary:["Дієта №5."]},
        {fName:"Петро", lName:"Шевченко", ward:103, diag:"Перелом стегна", meds:[{name:"Операція", dose:"остеосинтез", isN:false}, {name:"Промедол", dose:"1мл", isN:true}], diary:["Гіпсова пов'язка."]},
        {fName:"Олена", lName:"Мельник", ward:104, diag:"Холецистит", meds:[{name:"Клізма", dose:"сифонна", isN:false}], diary:["Скарги на нудоту."]},
        {fName:"Сергій", lName:"Ткач", ward:105, diag:"Виразка", meds:[{name:"Фентаніл", dose:"0.1мг", isN:true}], diary:["Суворий ліжковий режим."]},
        {fName:"Ганна", lName:"Мороз", ward:106, diag:"Грижа", meds:[{name:"Операція", dose:"планова", isN:false}], diary:["Обстежена."]},
        {fName:"Оксана", lName:"Поліщук", ward:107, diag:"Пневмонія", meds:[{name:"Цефтриаксон", dose:"1г", isN:false}, {name:"Анальгін", dose:"2.0", isN:false}], diary:["Температура 37.5."]},
        {fName:"Степан", lName:"Олійник", ward:108, diag:"Ниркова коліка", meds:[{name:"Морфін", dose:"10мг", isN:true}, {name:"Фуросемід", dose:"20мг", isN:false}], diary:["Спазмолітики введені."]}
    ];

    // Додаємо ще пацієнтів до 35 з різними даними
    const lastNames = ["Коваленко", "Бондаренко", "Кравченко", "Марченко", "Лисенко", "Савченко", "Дзюба", "Руденко", "Клименко"];
    for(let i=1; i<=27; i++) {
        patients.push({
            fName: "Пацієнт", lName: lastNames[i%lastNames.length] + " " + (10+i),
            ward: 110 + i,
            diag: i%3==0 ? "Забиття грудної клітки" : "Хронічний гастрит",
            meds: i%5==0 ? [{name:"Клізма", dose:"очисна", isN:false}] : [{name:"Вітаміни", dose:"B12", isN:false}],
            diary: ["Стан задовільний."]
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
        patients[curIdx].diary.forEach(e => d.innerHTML += `<div class="p-2 border-bottom bg-white mb-1 small">${e}</div>`);
    }
    function addNote() { const t = document.getElementById('cNote').value; if(t) { patients[curIdx].diary.push(t); document.getElementById('cNote').value = ""; updateDiary(); } }

    function updateChartMeds() {
        const a = document.getElementById('cMeds'); a.innerHTML = "";
        patients[curIdx].meds.forEach((m, idx) => {
            a.innerHTML += `<div class="d-flex justify-content-between p-2 mb-1 bg-white border rounded">
                <span class="${m.isN?'text-danger fw-bold':''}">${m.name} [${m.dose}]</span>
                <button class="btn btn-link text-danger btn-sm p-0" onclick="remM(${idx})">видалити</button>
            </div>`;
        });
    }

    function openAddMed() { new bootstrap.Modal(document.getElementById('medModal')).show(); }
    function fillM() { const v = document.getElementById('cat').value; if(v) { const [n,d] = v.split('|'); document.getElementById('iN').value=n; document.getElementById('iD').value=d; } }

    function processMed() {
        const n = document.getElementById('iN').value, d = document.getElementById('iD').value;
        const isN = ["Морфін", "Фентаніл", "Промедол"].some(nn => n.includes(nn));
        if(isN) {
            document.getElementById('alertMsg').innerText = `КОНТРОЛЬ ПКУ: ${n.toUpperCase()}`;
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
        if(confirm("Видалити запис?")) { patients[curIdx].meds.splice(idx,1); updateChartMeds(); render(); }
    }

    function closeAlert() { document.getElementById('narcoticOverlay').style.display = 'none'; }
    function addP() { 
        const ln = document.getElementById('ln').value, fn = document.getElementById('fn').value; 
        if(ln && fn) { 
            patients.unshift({fName:fn, lName:ln, ward:document.getElementById('wd').value, diag:document.getElementById('dg').value, meds:[], diary:[]}); 
            render(); 
        } 
    }
    function delP(i) { if(confirm("Виписати?")) { patients.splice(i,1); render(); } }

    render();
</script>
</body>
</html>
