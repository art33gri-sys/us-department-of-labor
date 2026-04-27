<!DOCTYPE html>
<html lang="uk">
<head>
    <meta charset="UTF-8">
    <title>HospitalOS v13.0 - Maximum Protocols</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        body { background-color: #f4f6f9; font-family: 'Segoe UI', sans-serif; font-size: 0.8rem; }
        .navbar { background-color: #004085; color: white; border-bottom: 3px solid #ffc107; }
        .sidebar-card { border: none; border-radius: 8px; box-shadow: 0 4px 12px rgba(0,0,0,0.1); background: white; }
        .badge-narcotic { background-color: #d90429; color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold; font-size: 0.65rem; text-transform: uppercase; }
        .badge-common { background-color: #1a7431; color: white; padding: 2px 5px; border-radius: 3px; font-size: 0.65rem; }
        .badge-proc { background-color: #6f42c1; color: white; padding: 2px 5px; border-radius: 3px; font-size: 0.65rem; }
        .scroll-area { max-height: 85vh; overflow-y: auto; border: 1px solid #ccc; background: white; }
        
        #narcoticOverlay {
            display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            z-index: 10000; background: rgba(140, 0, 0, 0.98); color: white; text-align: center; padding-top: 10%; 
        }
        .warning-box { border: 10px double white; display: inline-block; padding: 40px; border-radius: 20px; }
        .diag-edit { font-size: 1.1rem; font-weight: bold; color: #d90429; border: 2px solid #ddd; width: 100%; padding: 8px; }
    </style>
</head>
<body>

<div id="narcoticOverlay">
    <div class="warning-box shadow-lg">
        <h1 style="font-size: 5rem;">⚠️ ПКУ ⚠️</h1>
        <h2 id="alertMsg" class="mb-4">СУВОРИЙ ОБЛІК ПРЕПАРАТІВ</h2>
        <button id="confirmBtn" class="btn btn-light btn-lg fw-bold px-5 py-3">ПІДТВЕРДИТИ ВИТРАТУ</button>
        <button onclick="closeAlert()" class="btn btn-outline-light btn-lg ms-3">ВІДМІНА</button>
    </div>
</div>

<nav class="navbar navbar-dark mb-3 p-2">
    <div class="container-fluid px-4">
        <span class="navbar-brand fw-bold">🏥 HospitalOS v13.0 | Журнал призначень та процедур</span>
        <span id="stats">Завантаження бази...</span>
    </div>
</nav>

<div class="container-fluid px-4">
    <div class="row">
        <div class="col-lg-3">
            <div class="card p-3 sidebar-card mb-3">
                <h6 class="text-primary fw-bold">РЕЄСТРАЦІЯ</h6>
                <input type="text" id="ln" class="form-control form-control-sm mb-2" placeholder="Прізвище пацієнта">
                <input type="text" id="wd" class="form-control form-control-sm mb-2" placeholder="Палата №">
                <textarea id="dg" class="form-control form-control-sm mb-3" rows="3" placeholder="Клінічний діагноз..."></textarea>
                <button onclick="addP()" class="btn btn-primary btn-sm w-100 fw-bold">ГОСПІТАЛІЗУВАТИ</button>
            </div>
        </div>

        <div class="col-lg-9">
            <div class="card sidebar-card p-2">
                <div class="table-responsive scroll-area">
                    <table class="table table-sm table-hover align-middle mb-0">
                        <thead class="table-dark">
                            <tr>
                                <th width="60">Палата</th>
                                <th width="180">Прізвище</th>
                                <th>Діагноз</th>
                                <th>Листок призначень (Ліки та Процедури)</th>
                                <th width="100" class="text-end">Карта</th>
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
            <div class="modal-header bg-dark text-white">
                <h5 class="modal-title">📄 МЕДИЧНА КАРТА: <span id="cFull"></span></h5>
                <button type="button" class="btn-close btn-close-white" data-bs-dismiss="modal"></button>
            </div>
            <div class="modal-body p-4">
                <div class="row">
                    <div class="col-md-6 border-end">
                        <label class="fw-bold small">ДІАГНОЗ:</label>
                        <textarea id="cDiag" class="diag-edit mb-3" onchange="updateDiag()"></textarea>
                        
                        <label class="fw-bold small">ПРИЗНАЧЕННЯ ТА ПРОЦЕДУРИ:</label>
                        <div id="cMeds" class="bg-light p-2 rounded border mb-2" style="min-height: 250px; max-height: 350px; overflow-y: auto;"></div>
                        <button onclick="openAddMed()" class="btn btn-success btn-sm w-100 fw-bold">+ НОВЕ ПРИЗНАЧЕННЯ</button>
                    </div>
                    <div class="col-md-6">
                        <label class="fw-bold small">ЩОДЕННИК:</label>
                        <div id="cDiary" class="bg-light p-2 mb-2 border rounded" style="height: 280px; overflow-y: auto;"></div>
                        <div class="input-group">
                            <textarea id="cNote" class="form-control" placeholder="Новий запис..."></textarea>
                            <button onclick="addNote()" class="btn btn-primary">ОК</button>
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
            <div class="modal-body p-3">
                <label class="small fw-bold">Оберіть зі списку:</label>
                <select id="cat" class="form-select form-select-sm mb-2" onchange="fillM()">
                    <option value="">-- КАТАЛОГ --</option>
                    <optgroup label="НАРКОТИКИ (ПКУ)">
                        <option value="Морфін|1% 1.0|narc">Морфін</option>
                        <option value="Фентаніл|0.005% 2.0|narc">Фентаніл</option>
                        <option value="Промедол|2% 1.0|narc">Промедол</option>
                        <option value="Трамадол|50мг/мл|narc">Трамадол</option>
                    </optgroup>
                    <optgroup label="ПРОЦЕДУРИ">
                        <option value="Клізма|очисна|proc">Клізма очисна</option>
                        <option value="Клізма|сифонна|proc">Клізма сифонна</option>
                        <option value="Промивання шлунку|Cito!|proc">Промивання шлунку</option>
                        <option value="Встановлення катетера|Фолея|proc">Катетеризація</option>
                        <option value="Зондування|назогастральне|proc">Зондування</option>
                        <option value="Перев'язка|гнійна|proc">Перев'язка</option>
                        <option value="Операція|Лапаротомія|proc">Операція (ургентна)</option>
                    </optgroup>
                    <optgroup label="ПРЕПАРАТИ">
                        <option value="Цефтриаксон|1.0г в/м|common">Цефтриаксон</option>
                        <option value="Метронідазол|100.0 в/в|common">Метронідазол</option>
                        <option value="Адреналін|0.1% 1.0|common">Адреналін</option>
                        <option value="Дофамін|4% 5.0|common">Дофамін</option>
                        <option value="Фуросемід|1% 2.0|common">Фуросемід</option>
                        <option value="Гепарин|5000 ОД|common">Гепарин</option>
                    </optgroup>
                </select>
                <input type="text" id="iN" class="form-control form-control-sm mb-1" placeholder="Назва">
                <input type="text" id="iD" class="form-control form-control-sm mb-2" placeholder="Доза/Режим">
                <button onclick="processMed()" class="btn btn-primary btn-sm w-100">ДОДАТИ</button>
            </div>
        </div>
    </div>
</div>

<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
<script>
    const surnames = ["КОВАЛЕНКО", "МЕЛЬНИК", "БОНДАРЕНКО", "ТКАЧЕНКО", "ШЕВЧЕНКО", "ОЛІЙНИК", "ПОЛІЩУК", "КРАВЧЕНКО", "КОЗАК", "МОРОЗ", "РУДЕНКО", "ЛИСЕНКО", "ПАВЛЕНКО", "САВЧЕНКО", "ДЗЮБА", "ГОНЧАР", "МАРТИНЮК", "КЛИМЕНКО", "ВАКУЛЕНКО", "АНДРІЄНКО"];
    
    const diags = [
        "Гострий перитоніт, сепсис", "Шлункова кровотеча, виразка", "Інфаркт міокарда, ГН", "Політравма, ДТП", "Панкреонекроз", "Кишкова непрохідність", "Гангрена нижньої кінцівки", "Аневризма аорти", "Ниркова недостатність", "Абсцес черевної порожнини"
    ];

    const drugs = [
        {n:"Морфін", d:"10мг", t:"narc"}, {n:"Фентаніл", d:"0.1мг", t:"narc"}, {n:"Клізма", d:"очисна", t:"proc"},
        {n:"Операція", d:"Cito!", t:"proc"}, {n:"Цефтриаксон", d:"1г", t:"common"}, {n:"Промивання шлунку", d:"терміново", t:"proc"},
        {n:"Гепарин", d:"5000 ОД", t:"common"}, {n:"Зонд", d:"встановлено", t:"proc"}, {n:"Фуросемід", d:"20мг", t:"common"},
        {n:"Перев'язка", d:"щоденно", t:"proc"}, {n:"Дофамін", d:"в/в крап", t:"common"}, {n:"Катетер", d:"Фолея", t:"proc"}
    ];

    let patients = [];

    // ГЕНЕРУЄМО 35 ПАЦІЄНТІВ З ВЕЛИКИМИ СПИСКАМИ
    for(let i=1; i<=35; i++){
        let pMeds = [];
        // Кожному даємо 4-6 випадкових призначень
        let count = 4 + Math.floor(Math.random()*3);
        for(let j=0; j<count; j++){
            let randomDrug = drugs[Math.floor(Math.random()*drugs.length)];
            if(!pMeds.some(m => m.name === randomDrug.n)) {
                pMeds.push({name: randomDrug.n, dose: randomDrug.d, type: randomDrug.t});
            }
        }
        
        patients.push({
            lName: surnames[Math.floor(Math.random()*surnames.length)] + " " + (1000 + i),
            ward: 200 + i,
            diag: diags[Math.floor(Math.random()*diags.length)],
            meds: pMeds,
            diary: ["Стан важкий. Потребує моніторингу.", "Призначено інтенсивну терапію."]
        });
    }

    let curIdx = null;

    function render() {
        const b = document.getElementById('pt'); b.innerHTML = "";
        document.getElementById('stats').innerText = `Всього: ${patients.length} | ПКУ: ${patients.reduce((a,b) => a + b.meds.filter(m=>m.type==='narc').length, 0)}`;
        
        patients.forEach((p, i) => {
            let mStr = p.meds.map(m => {
                let cls = m.type==='narc' ? 'badge-narcotic' : (m.type==='proc' ? 'badge-proc' : 'badge-common');
                return `<span class="${cls} me-1 mb-1 d-inline-block">${m.name}</span>`;
            }).join("");
            
            b.innerHTML += `<tr>
                <td><span class="badge bg-secondary">№${p.ward}</span></td>
                <td class="fw-bold text-uppercase">${p.lName}</td>
                <td><small class="text-danger fw-bold">${p.diag}</small></td>
                <td>${mStr}</td>
                <td class="text-end">
                    <button onclick="openChart(${i})" class="btn btn-sm btn-dark">КАРТА</button>
                </td>
            </tr>`;
        });
    }

    function openChart(i) {
        curIdx = i; const p = patients[i];
        document.getElementById('cFull').innerText = p.lName;
        document.getElementById('cDiag').value = p.diag;
        updateChartMeds(); updateDiary();
        new bootstrap.Modal(document.getElementById('chartModal')).show();
    }

    function updateDiag() { patients[curIdx].diag = document.getElementById('cDiag').value; render(); }
    function updateDiary() { 
        const d = document.getElementById('cDiary'); d.innerHTML = "";
        patients[curIdx].diary.forEach(e => d.innerHTML += `<div class="p-2 border-bottom mb-1 bg-white small shadow-sm">${e}</div>`);
    }
    function addNote() { const t = document.getElementById('cNote').value; if(t) { patients[curIdx].diary.push(t); document.getElementById('cNote').value = ""; updateDiary(); } }

    function updateChartMeds() {
        const a = document.getElementById('cMeds'); a.innerHTML = "";
        patients[curIdx].meds.forEach((m, idx) => {
            let color = m.type==='narc' ? 'text-danger fw-bold' : (m.type==='proc' ? 'text-primary' : 'text-success');
            a.innerHTML += `<div class="d-flex justify-content-between p-2 mb-1 bg-white border rounded">
                <span class="${color}">${m.name} — ${m.dose}</span>
                <button class="btn btn-link text-danger btn-sm p-0 text-decoration-none" onclick="remM(${idx})">видалити</button>
            </div>`;
        });
    }

    function openAddMed() { new bootstrap.Modal(document.getElementById('medModal')).show(); }
    function fillM() { const v = document.getElementById('cat').value; if(v) { const [n,d] = v.split('|'); document.getElementById('iN').value=n; document.getElementById('iD').value=d; } }

    function processMed() {
        const n = document.getElementById('iN').value, d = document.getElementById('iD').value;
        const sel = document.getElementById('cat').options[document.getElementById('cat').selectedIndex];
        const type = sel.parentElement.label === "НАРКОТИКИ (ПКУ)" ? 'narc' : (sel.parentElement.label === "ПРОЦЕДУРИ" ? 'proc' : 'common');

        if(type === 'narc') {
            document.getElementById('alertMsg').innerText = `ВИТРАТА ПКУ: ${n.toUpperCase()} (${d})`;
            document.getElementById('narcoticOverlay').style.display = 'block';
            document.getElementById('confirmBtn').onclick = () => { saveM(n,d,type); closeAlert(); };
        } else { saveM(n,d,type); }
    }

    function saveM(n,d,t) {
        if(n && d) {
            patients[curIdx].meds.push({name:n, dose:d, type:t});
            updateChartMeds(); render();
            bootstrap.Modal.getInstance(document.getElementById('medModal')).hide();
        }
    }

    function remM(idx) {
        if(confirm("Скасувати призначення/процедуру?")) { patients[curIdx].meds.splice(idx,1); updateChartMeds(); render(); }
    }

    function closeAlert() { document.getElementById('narcoticOverlay').style.display = 'none'; }
    function addP() { 
        const ln = document.getElementById('ln').value; 
        if(ln) { 
            patients.unshift({lName:ln, ward:document.getElementById('wd').value, diag:document.getElementById('dg').value, meds:[], diary:[]}); 
            render(); 
        } 
    }

    render();
</script>
</body>
</html>
