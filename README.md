<!DOCTYPE html>
<html lang="uk">
<head>
    <meta charset="UTF-8">
    <title>МІС HospitalOS v7.0 - Електронна Картка</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        body { background-color: #f1f3f5; font-family: 'Segoe UI', sans-serif; font-size: 0.85rem; }
        .navbar { background-color: #004085; border-bottom: 3px solid #ffc107; }
        .hospital-card { border: none; border-radius: 10px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); background: white; }
        .badge-narcotic { background-color: #d63384; color: white; padding: 3px 6px; border-radius: 4px; font-weight: bold; }
        .badge-common { background-color: #198754; color: white; padding: 3px 6px; border-radius: 4px; }
        .scroll-area { max-height: 75vh; overflow-y: auto; border-radius: 8px; border: 1px solid #dee2e6; }
        
        /* OVERLAYS */
        #dangerOverlay {
            display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            z-index: 9999; color: white; text-align: center; padding-top: 10%;
            background: rgba(139, 0, 0, 0.97);
        }
        .diary-entry { background: #f8f9fa; border-left: 4px solid #007bff; padding: 8px; margin-bottom: 5px; border-radius: 4px; }
    </style>
</head>
<body>

<div id="dangerOverlay">
    <div class="p-5 border border-white d-inline-block rounded-4">
        <h1 style="font-size: 4rem;">⚠️ УВАГА!</h1>
        <p id="alertMsg" class="fs-2 my-4"></p>
        <button id="confirmBtn" class="btn btn-light btn-lg fw-bold px-5 py-3">ПІДТВЕРДЖУЮ</button>
        <button onclick="closeAlert()" class="btn btn-outline-light btn-lg ms-3">СКАСУВАТИ</button>
    </div>
</div>

<nav class="navbar navbar-dark p-2 mb-4">
    <div class="container-fluid">
        <span class="navbar-brand fw-bold">🏥 HospitalOS Pro | <small>Реєстр ЕМК</small></span>
    </div>
</nav>

<div class="container-fluid px-4">
    <div class="row g-4">
        <div class="col-lg-3">
            <div class="card p-3 hospital-card">
                <h6 class="text-primary fw-bold">Прийомний відділ</h6>
                <hr>
                <input type="text" id="ln" class="form-control form-control-sm mb-2" placeholder="Прізвище">
                <input type="text" id="fn" class="form-control form-control-sm mb-2" placeholder="Ім'я">
                <input type="text" id="wd" class="form-control form-control-sm mb-2" placeholder="Палата">
                <textarea id="dg" class="form-control form-control-sm mb-3" placeholder="Первинний діагноз"></textarea>
                <button onclick="addP()" class="btn btn-primary btn-sm w-100">Госпіталізувати</button>
            </div>
        </div>

        <div class="col-lg-9">
            <div class="card p-2 hospital-card">
                <div class="table-responsive scroll-area">
                    <table class="table table-sm table-hover align-middle">
                        <thead class="table-light">
                            <tr>
                                <th>Палата</th>
                                <th>Пацієнт</th>
                                <th>Діагноз</th>
                                <th>Призначення</th>
                                <th class="text-end">Медична карта</th>
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
    <div class="modal-dialog modal-lg modal-dialog-scrollable">
        <div class="modal-content">
            <div class="modal-header bg-primary text-white">
                <h5 class="modal-title">📄 ЕМК пацієнта: <span id="cFull"></span></h5>
                <button type="button" class="btn-close btn-close-white" data-bs-dismiss="modal"></button>
            </div>
            <div class="modal-body bg-light">
                <div class="row">
                    <div class="col-md-5">
                        <div class="card p-3 mb-3 shadow-sm">
                            <h6 class="fw-bold">Поточне лікування</h6>
                            <div id="cMeds"></div>
                            <hr>
                            <button onclick="openAddMed()" class="btn btn-success btn-sm w-100">+ Призначити ліки</button>
                        </div>
                    </div>
                    <div class="col-md-7">
                        <div class="card p-3 shadow-sm">
                            <h6 class="fw-bold">Щоденник спостереження (Коментарі)</h6>
                            <div id="cDiary" class="mb-3" style="max-height: 200px; overflow-y: auto;"></div>
                            <div class="input-group">
                                <textarea id="cNote" class="form-control" rows="2" placeholder="Запис лікаря..."></textarea>
                                <button onclick="addNote()" class="btn btn-primary">Зберегти</button>
                            </div>
                        </div>
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
                <label class="fw-bold small">Каталог препаратів:</label>
                <select id="cat" class="form-select mb-2" onchange="fillM()">
                    <option value="">-- Оберіть --</option>
                    <option value="Морфін|10мг|Наркотичне">Морфін ⚠️</option>
                    <option value="Фентаніл|2мл|Наркотичне">Фентаніл ⚠️</option>
                    <option value="Цефтриаксон|1г|Звичайне">Цефтриаксон</option>
                    <option value="Дексаметазон|4мг|Звичайне">Дексаметазон</option>
                </select>
                <input type="text" id="iN" class="form-control mb-1" placeholder="Назва">
                <input type="text" id="iD" class="form-control mb-1" placeholder="Доза">
                <select id="iT" class="form-select mb-3">
                    <option value="Звичайне">Звичайне</option>
                    <option value="Наркотичне">Наркотичне</option>
                </select>
                <button onclick="checkN()" class="btn btn-success w-100">Підписати</button>
            </div>
        </div>
    </div>
</div>

<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
<script>
    let patients = [
        {fName: "Олександр", lName: "Шевченко", ward: "101", diag: "Інфаркт", meds: [], diary: ["Пацієнт поступив зі скаргами на біль у грудях."]},
        {fName: "Марія", lName: "Коваленко", ward: "102", diag: "Пневмонія", meds: [], diary: ["Температура 38.5, кашель."]}
    ];

    // Автозаповнення до 35 осіб
    for(let i=1; i<=33; i++) {
        patients.push({
            fName: "Пацієнт", lName: "Прізвище №"+i, ward: 102+i, 
            diag: "Плановий огляд", meds: [], diary: ["Статус стабільний."]
        });
    }

    let curIdx = null;

    function render() {
        const b = document.getElementById('pt'); b.innerHTML = "";
        patients.forEach((p, i) => {
            let mStr = p.meds.map(m => `<span class="${m.type==='Наркотичне'?'badge-narcotic':'badge-common'} me-1">${m.name}</span>`).join("");
            b.innerHTML += `<tr>
                <td><span class="badge bg-secondary">${p.ward}</span></td>
                <td><b>${p.lName}</b> ${p.fName}</td>
                <td><small>${p.diag}</small></td>
                <td>${mStr}</td>
                <td class="text-end">
                    <button onclick="openChart(${i})" class="btn btn-sm btn-info text-white">📄 Картка</button>
                    <button onclick="delP(${i})" class="btn btn-sm btn-outline-danger">✕</button>
                </td>
            </tr>`;
        });
    }

    function openChart(i) {
        curIdx = i;
        const p = patients[i];
        document.getElementById('cFull').innerText = p.lName + " " + p.fName;
        updateChartMeds();
        updateDiary();
        new bootstrap.Modal(document.getElementById('chartModal')).show();
    }

    function updateDiary() {
        const d = document.getElementById('cDiary'); d.innerHTML = "";
        patients[curIdx].diary.forEach(entry => {
            d.innerHTML += `<div class="diary-entry small">${entry}</div>`;
        });
    }

    function addNote() {
        const n = document.getElementById('cNote').value;
        if(n) {
            patients[curIdx].diary.push(n);
            document.getElementById('cNote').value = "";
            updateDiary();
        }
    }

    function updateChartMeds() {
        const mArea = document.getElementById('cMeds'); mArea.innerHTML = "";
        patients[curIdx].meds.forEach((m, mIdx) => {
            mArea.innerHTML += `<div class="d-flex justify-content-between border-bottom py-1">
                <span class="${m.type==='Наркотическое'?'text-danger fw-bold':''}">${m.name} (${m.dose})</span>
                <button class="btn btn-link btn-sm text-danger p-0" onclick="remMed(${mIdx})">видалити</button>
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
            showAlert(`ВИПИСКА НАРКОТИЧНОГО ЗАСОБУ: ${n}`, () => {
                patients[curIdx].meds.push({name:n, dose:d, type:t});
                updateChartMeds(); render();
                bootstrap.Modal.getInstance(document.getElementById('medModal')).hide();
            });
        } else {
            patients[curIdx].meds.push({name:n, dose:d, type:t});
            updateChartMeds(); render();
            bootstrap.Modal.getInstance(document.getElementById('medModal')).hide();
        }
    }

    function remMed(mIdx) {
        if(confirm("Видалити призначення?")) {
            patients[curIdx].meds.splice(mIdx, 1);
            updateChartMeds(); render();
        }
    }

    function showAlert(msg, onC) {
        document.getElementById('alertMsg').innerText = msg;
        document.getElementById('dangerOverlay').style.display = 'block';
        document.getElementById('confirmBtn').onclick = () => { onC(); closeAlert(); };
    }

    function closeAlert() { document.getElementById('dangerOverlay').style.display = 'none'; }
    function addP() {
        const ln = document.getElementById('ln').value, fn = document.getElementById('fn').value;
        if(ln && fn) {
            patients.unshift({fName:fn, lName:ln, ward:document.getElementById('wd').value, diag:document.getElementById('dg').value, meds:[], diary:[]});
            render();
        }
    }
    function delP(i) { if(confirm("Виписати пацієнта?")) { patients.splice(i,1); render(); } }

    render();
</script>
</body>
</html>
