<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>HospitalOS v5.5 - STRICT CONTROL</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        body { background-color: #f4f7f9; font-family: 'Segoe UI', sans-serif; font-size: 0.8rem; }
        .hospital-card { border: none; border-radius: 10px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); }
        .table thead { background-color: #2c3e50; color: white; position: sticky; top: 0; z-index: 10; }
        .badge-narcotic { background-color: #ff0055; color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold; animation: pulse 2s infinite; }
        @keyframes pulse { 0% { opacity: 1; } 50% { opacity: 0.7; } 100% { opacity: 1; } }
        .badge-common { background-color: #2a9d8f; color: white; padding: 2px 5px; border-radius: 3px; }
        .scroll-area { max-height: 80vh; overflow-y: auto; background: white; border: 1px solid #ccc; border-radius: 8px; }
        
        /* ЭКРАН ПРЕДУПРЕЖДЕНИЯ */
        #narcoticOverlay {
            display: none;
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(139, 0, 0, 0.95);
            z-index: 9999;
            color: white;
            text-align: center;
            padding-top: 15%;
            font-family: 'Arial Black', sans-serif;
        }
        .warning-box { border: 5px solid white; display: inline-block; padding: 40px; border-radius: 20px; }
    </style>
</head>
<body>

<div id="narcoticOverlay">
    <div class="warning-box">
        <h1 style="font-size: 4rem;">⚠️ ВНИМАНИЕ! ⚠️</h1>
        <h2 class="mb-4">ВЫПИСКА НАРКОТИЧЕСКОГО ПРЕПАРАТА</h2>
        <p style="font-size: 1.5rem;">Препарат: <span id="warnMed"></span> | Доза: <span id="warnDose"></span></p>
        <p class="text-warning">Данное действие будет занесено в федеральный реестр учета.<br>Требуется наличие сейфа и подписи зав. отделением.</p>
        <hr border="white">
        <button onclick="confirmNarcotic()" class="btn btn-light btn-lg fw-bold px-5">Я ПОДТВЕРЖДАЮ ОТВЕТСТВЕННОСТЬ</button>
        <button onclick="cancelNarcotic()" class="btn btn-outline-light btn-lg ms-3">ОТМЕНА</button>
    </div>
</div>

<nav class="navbar navbar-dark bg-dark mb-3">
    <div class="container-fluid px-4">
        <span class="navbar-brand">🏥 <b>HospitalOS v5.5</b> | Пост ПКУ (Предметно-количественный учет)</span>
    </div>
</nav>

<div class="container-fluid px-4">
    <div class="row">
        <div class="col-lg-3">
            <div class="card p-3 hospital-card shadow-sm mb-4">
                <h6 class="text-primary border-bottom pb-2">Приемный покой</h6>
                <input type="text" id="ln" class="form-control form-control-sm mb-2" placeholder="Фамилия">
                <input type="text" id="fn" class="form-control form-control-sm mb-2" placeholder="Имя">
                <input type="text" id="wd" class="form-control form-control-sm mb-2" placeholder="Палата">
                <textarea id="dg" class="form-control form-control-sm mb-3" placeholder="Диагноз"></textarea>
                <button onclick="addP()" class="btn btn-primary btn-sm w-100">Госпитализировать</button>
            </div>
        </div>

        <div class="col-lg-9">
            <div class="card p-2 hospital-card">
                <div class="table-responsive scroll-area">
                    <table class="table table-sm table-hover align-middle">
                        <thead>
                            <tr>
                                <th width="80">Палата</th>
                                <th>Пациент</th>
                                <th>Диагноз</th>
                                <th>Назначения</th>
                                <th class="text-end">Опции</th>
                            </tr>
                        </thead>
                        <tbody id="pt"></tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
</div>

<div class="modal fade" id="mMod" tabindex="-1">
    <div class="modal-dialog modal-dialog-centered">
        <div class="modal-content">
            <div class="modal-body p-4">
                <h6 class="mb-3">Лист назначений: <b id="mPat" class="text-primary"></b></h6>
                <div class="row g-2">
                    <div class="col-6">
                        <label class="small fw-bold">Из каталога:</label>
                        <select id="cat" class="form-select form-select-sm" onchange="fill()">
                            <option value="">-- Выбрать --</option>
                            <option value="Морфин|1.0|Наркотическое">Морфин ⚠️</option>
                            <option value="Фентанил|0.05мг|Наркотическое">Фентанил ⚠️</option>
                            <option value="Промедол|2%|Наркотическое">Промедол ⚠️</option>
                            <option value="Сибазон|2мл|Наркотическое">Сибазон ⚠️</option>
                            <option value="Анальгин|2.0|Обычное">Анальгин</option>
                            <option value="Дексаметазон|4мг|Обычное">Дексаметазон</option>
                        </select>
                    </div>
                    <div class="col-6">
                        <label class="small fw-bold">Ручной ввод:</label>
                        <input type="text" id="iN" class="form-control form-control-sm mb-1" placeholder="Препарат">
                        <input type="text" id="iD" class="form-control form-control-sm mb-1" placeholder="Доза">
                        <select id="iT" class="form-select form-select-sm">
                            <option value="Обычное">Обычное</option>
                            <option value="Наркотическое">Наркотическое</option>
                        </select>
                    </div>
                </div>
                <button onclick="preSave()" class="btn btn-success btn-sm w-100 mt-3 shadow">Подтвердить назначение</button>
            </div>
        </div>
    </div>
</div>

<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
<script>
    let patients = [
        {fName: "Владимир", lName: "Соловьев", ward: "101", diag: "Почечная колика", meds: []},
        {fName: "Александр", lName: "Громов", ward: "102", diag: "Отек легких", meds: []}
    ];
    // Добавим еще 33 для массовки
    for(let i=1; i<=33; i++) {
        patients.push({fName: "Пациент", lName: "№"+i, ward: 102+i, diag: "Плановое обследование", meds: []});
    }

    let curIdx = null;

    function render() {
        const b = document.getElementById('pt'); b.innerHTML = "";
        patients.forEach((p, i) => {
            let ms = p.meds.map(m => `<span class="${m.type==='Наркотическое'?'badge-narcotic':'badge-common'} me-1 mb-1 d-inline-block">${m.name}</span>`).join("");
            b.innerHTML += `<tr>
                <td><span class="badge bg-primary">П-${p.ward}</span></td>
                <td><b>${p.lName}</b> ${p.fName}</td>
                <td><small>${p.diag}</small></td>
                <td>${ms}</td>
                <td class="text-end">
                    <button onclick="op(${i})" class="btn btn-sm btn-dark py-0">💊</button>
                    <button onclick="del(${i})" class="btn btn-sm btn-outline-danger py-0">✕</button>
                </td>
            </tr>`;
        });
    }

    function addP() {
        const ln = document.getElementById('ln').value; const fn = document.getElementById('fn').value;
        const wd = document.getElementById('wd').value; const dg = document.getElementById('dg').value;
        if(ln && fn) { patients.unshift({fName:fn, lName:ln, ward:wd, diag:dg, meds:[]}); render(); }
    }

    function op(i) {
        curIdx = i; document.getElementById('mPat').innerText = patients[i].lName;
        new bootstrap.Modal(document.getElementById('mMod')).show();
    }

    function fill() {
        const v = document.getElementById('cat').value;
        if(v) { const [n,d,t] = v.split('|'); document.getElementById('iN').value=n; document.getElementById('iD').value=d; document.getElementById('iT').value=t; }
    }

    // ПРОВЕРКА ПЕРЕД СОХРАНЕНИЕМ
    function preSave() {
        const name = document.getElementById('iN').value;
        const dose = document.getElementById('iD').value;
        const type = document.getElementById('iT').value;

        if(type === 'Наркотическое') {
            document.getElementById('warnMed').innerText = name;
            document.getElementById('warnDose').innerText = dose;
            document.getElementById('narcoticOverlay').style.display = "block";
        } else {
            finalizeSave(name, dose, type);
        }
    }

    function confirmNarcotic() {
        const name = document.getElementById('iN').value;
        const dose = document.getElementById('iD').value;
        const type = document.getElementById('iT').value;
        document.getElementById('narcoticOverlay').style.display = "none";
        finalizeSave(name, dose, type);
    }

    function cancelNarcotic() {
        document.getElementById('narcoticOverlay').style.display = "none";
    }

    function finalizeSave(n, d, t) {
        if(n && d) {
            patients[curIdx].meds.push({name: n, dose: d, type: t});
            render();
            bootstrap.Modal.getInstance(document.getElementById('mMod')).hide();
        }
    }

    function del(i) { if(confirm("Выписать?")) { patients.splice(i,1); render(); } }
    render();
</script>
</body>
</html>
