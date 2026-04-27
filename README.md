<!DOCTYPE html>
<html lang="uk">
<head>
    <meta charset="UTF-8">
    <title>HospitalOS v9.0 - Strict Control & Edit</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        body { background-color: #f0f4f8; font-family: 'Segoe UI', sans-serif; font-size: 0.85rem; }
        .navbar { background-color: #003366; border-bottom: 4px solid #cc0000; }
        .hospital-card { border: none; border-radius: 10px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); background: white; }
        .badge-narcotic { background-color: #ff0000; color: white; padding: 4px 8px; border-radius: 4px; font-weight: bold; text-transform: uppercase; font-size: 0.7rem; }
        .badge-common { background-color: #28a745; color: white; padding: 4px 8px; border-radius: 4px; }
        .scroll-area { max-height: 75vh; overflow-y: auto; background: white; border-radius: 10px; border: 1px solid #ced4da; }
        
        /* КАРТКА ПАЦІЄНТА */
        .medical-form { background: #ffffff; padding: 30px; border-radius: 0 0 10px 10px; }
        .diag-input { font-size: 1.2rem; font-weight: bold; color: #d9534f; border: 2px solid #eee; border-radius: 5px; padding: 5px; width: 100%; }
        .diag-input:focus { border-color: #d9534f; outline: none; background: #fff5f5; }

        /* ЕКРАН ТРИВОГИ */
        #dangerOverlay {
            display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            z-index: 10000; color: white; text-align: center; padding-top: 10%;
            background: rgba(180, 0, 0, 0.98); animation: flash 0.5s infinite;
        }
        @keyframes flash { 0% { background: rgba(180, 0, 0, 0.98); } 50% { background: rgba(100, 0, 0, 0.98); } }
        .warning-box { border: 8px solid white; display: inline-block; padding: 50px; border-radius: 20px; }
    </style>
</head>
<body>

<div id="dangerOverlay">
    <div class="warning-box shadow-lg">
        <h1 style="font-size: 5rem;">⚠️ ПКУ ⚠️</h1>
        <h2 class="mb-4">КОНТРОЛЬ НАРКОТИЧНИХ ЗАСОБІВ</h2>
        <p id="alertMsg" class="fs-2"></p>
        <div class="mt-5">
            <button id="confirmBtn" class="btn btn-light btn-lg fw-bold px-5 py-3">ПІДТВЕРДИТИ ВИТРАТУ</button>
            <button onclick="closeAlert()" class="btn btn-outline-light btn-lg ms-3">СКАСУВАТИ</button>
        </div>
    </div>
</div>

<nav class="navbar navbar-dark p-2">
    <div class="container-fluid px-4">
        <span class="navbar-brand fw-bold">🏥 МІС «HospitalOS» v9.0 | СУВОРИЙ ОБЛІК</span>
    </div>
</nav>

<div class="container-fluid mt-3 px-4">
    <div class="row g-3">
        <div class="col-lg-3">
            <div class="card p-3 hospital-card">
                <h6 class="fw-bold text-danger">НОВИЙ ПАЦІЄНТ</h6>
                <hr>
                <input type="text" id="ln" class="form-control form-control-sm mb-2" placeholder="Прізвище">
                <input type="text" id="fn" class="form-control form-control-sm mb-2" placeholder="Ім'я">
                <input type="text" id="wd" class="form-control form-control-sm mb-2" placeholder="Палата">
                <textarea id="dg" class="form-control form-control-sm mb-3" placeholder="Попередній діагноз"></textarea>
                <button onclick="addP()" class="btn btn-danger btn-sm w-100 fw-bold">ЗАРЕЄСТРУВАТИ</button>
            </div>
        </div>

        <div class="col-lg-9">
            <div class="card p-2 hospital-card">
                <div class="table-responsive scroll-area">
                    <table class="table table-hover align-middle mb-0">
                        <thead class="table-dark">
                            <tr>
                                <th>Палата</th>
                                <th>Пацієнт</th>
                                <th>Діагноз</th>
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
            <div class="modal-header bg-danger text-white">
                <h5 class="modal-title">📄 МЕДИЧНА КАРТА: <span id="cFull"></span></h5>
                <button type="button" class="btn-close btn-close-white" data-bs-dismiss="modal"></button>
            </div>
            <div class="modal-body medical-form">
                <div class="row">
                    <div class="col-md-5 border-end">
                        <label class="fw-bold small text-muted">КЛІНІЧНИЙ ДІАГНОЗ (можна редагувати):</label>
                        <input type="text" id="cDiag" class="diag-input mb-3" onchange="updateDiag()">
                        
                        <label class="fw-bold small text-muted">ЛИСТОК ПРИЗНАЧЕНЬ:</label>
                        <div id="cMeds" class="mt-2 border rounded p-2 bg-light" style="min-height: 150px;"></div>
                        <button onclick="openAddMed()" class="btn btn-dark btn-sm w-100 mt-3">+ ДОДАТИ ПРЕПАРАТ</button>
                    </div>
                    <div class="col-md-7">
                        <label class="fw-bold small text-muted">ЩОДЕННИК ЛІКАРЯ:</label>
                        <div id="cDiary" class="mb-2 p-2 border rounded bg-light" style="height: 200px; overflow-y: auto;"></div>
                        <textarea id="cNote" class="form-control" rows="3" placeholder="Додати запис про стан пацієнта..."></textarea>
                        <button onclick="addNote()" class="btn btn-primary mt-2 w-100">ЗБЕРЕГТИ ЗАПИС</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

<div class="modal fade" id="medModal" tabindex="-1">
    <div class="modal-dialog modal-sm modal-dialog-centered">
        <div class="modal-content shadow-lg">
            <div class="modal-body">
                <h6 class="fw-bold">Призначення препарату</h6>
                <select id="cat" class="form-select mb-2" onchange="fillM()">
                    <option value="">-- Оберіть зі списку --</option>
                    <option value="Морфін|10мг">МОРФІН (Наркотичне) ⚠️</option>
                    <option value="Фентаніл|0.1мг">ФЕНТАНІЛ (Наркотичне) ⚠️</option>
                    <option value="Налбуфін|20мг">НАЛБУФІН (Наркотичне) ⚠️</option>
                    <option value="Промедол|1мл">ПРОМЕДОЛ (Наркотичне) ⚠️</option>
                    <option value="Сибазон|2мл">СИБАЗОН (Психотропне) ⚠️</option>
                    <option value="Цефтриаксон|1.0г">Цефтриаксон</option>
                    <option value="Дексаметазон|4мг">Дексаметазон</option>
                </select>
                <input type="text" id="iN" class="form-control mb-1" placeholder="Назва ліків">
                <input type="text" id="iD" class="form-control mb-3" placeholder="Доза">
                <button onclick="processMed()" class="btn btn-danger w-100 fw-bold">ПІДПИСАТИ</button>
            </div>
        </div>
    </div>
</div>

<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
<script>
    // Список наркотичних речовин для авто-перевірки
    const narcList = ["Морфін", "Фентаніл", "Налбуфін", "Промедол", "Сибазон", "Трамадол", "Кетамін"];

    let patients = [];
    const names = ["Іван", "Петро", "Олена", "Марія", "Степан", "Василь", "Анна", "Тетяна", "Микола", "Андрій"];
    const surnames = ["Коваленко", "Бондар", "Шевченко", "Мельник", "Ткач", "Козак", "Олійник", "Мороз", "Лисенко", "Кравченко"];

    for(let i=1; i<=35; i++){
        patients.push({
            fName: names[Math.floor(Math.random()*names.length)],
            lName: surnames[Math.floor(Math.random()*surnames.length)],
            ward: 100 + i,
            diag: "Попередній діагноз №" + i,
            meds: [],
            diary: ["Пацієнт госпіталізований."]
        });
    }

    let curIdx = null;

    function render() {
        const b = document.getElementById('pt'); b.innerHTML = "";
        patients.forEach((p, i) => {
            let mStr = p.meds.map(m => `<span class="${m.isNarc?'badge-narcotic':'badge-common'} me-1 mb-1 d-inline-block">${m.name}</span>`).join("");
            b.innerHTML += `<tr>
                <td><span class="badge bg-secondary">П-${p.ward}</span></td>
                <td><b>${p.lName}</b> ${p.fName}</td>
                <td><small class="text-danger fw-bold">${p.diag}</small></td>
                <td>${mStr}</td>
                <td class="text-end">
                    <button onclick="openChart(${i})" class="btn btn-sm btn-outline-dark">📄 КАРТА</button>
                    <button onclick="delP(${i})" class="btn btn-sm btn-light">✕</button>
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

    function updateDiag() {
        patients[curIdx].diag = document.getElementById('cDiag').value;
        render();
    }

    function updateDiary() {
        const d = document.getElementById('cDiary'); d.innerHTML = "";
        patients[curIdx].diary.forEach(e => d.innerHTML += `<div class="p-2 border-bottom small">${e}</div>`);
    }

    function addNote() {
        const t = document.getElementById('cNote').value;
        if(t) { patients[curIdx].diary.push(t); document.getElementById('cNote').value = ""; updateDiary(); }
    }

    function updateChartMeds() {
        const a = document.getElementById('cMeds'); a.innerHTML = "";
        patients[curIdx].meds.forEach((m, idx) => {
            a.innerHTML += `<div class="d-flex justify-content-between p-1 border-bottom">
                <span class="${m.isNarc?'text-danger fw-bold':''}">${m.name} (${m.dose})</span>
                <button class="btn btn-sm text-muted" onclick="remM(${idx})">видалити</button>
            </div>`;
        });
    }

    function openAddMed() { new bootstrap.Modal(document.getElementById('medModal')).show(); }

    function fillM() {
        const v = document.getElementById('cat').value;
        if(v) { const [n,d] = v.split('|'); document.getElementById('iN').value=n; document.getElementById('iD').value=d; }
    }

    // ГОЛОВНА ФУНКЦІЯ ПЕРЕВІРКИ
    function processMed() {
        const n = document.getElementById('iN').value;
        const d = document.getElementById('iD').value;
        
        // Авто-перевірка на наркотик (якщо назва містить слово зі списку)
        const isNarcotic = narcList.some(narc => n.toLowerCase().includes(narc.toLowerCase()));

        if(isNarcotic) {
            document.getElementById('alertMsg').innerText = `РЕЄСТРАЦІЯ ВИКОРИСТАННЯ: ${n.toUpperCase()} - ${d}`;
            document.getElementById('dangerOverlay').style.display = 'block';
            document.getElementById('confirmBtn').onclick = () => { saveM(n, d, true); closeAlert(); };
        } else {
            saveM(n, d, false);
        }
    }

    function saveM(n, d, isNarc) {
        if(n && d) {
            patients[curIdx].meds.push({name:n, dose:d, isNarc: isNarc});
            updateChartMeds(); render();
            bootstrap.Modal.getInstance(document.getElementById('medModal')).hide();
        }
    }

    function remM(idx) {
        const m = patients[curIdx].meds[idx];
        const msg = m.isNarc ? "УВАГА! ВИДАЛЕННЯ НАРКОТИЧНОГО ЗАСОБУ З ПРИЗНАЧЕНЬ!" : "Видалити ліки?";
        if(confirm(msg)) { patients[curIdx].meds.splice(idx,1); updateChartMeds(); render(); }
    }

    function closeAlert() { document.getElementById('dangerOverlay').style.display = 'none'; }
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
