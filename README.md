<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>HospitalOS v4.0 - Full Control</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        body { background-color: #f0f2f5; font-family: 'Segoe UI', sans-serif; font-size: 0.85rem; }
        .hospital-card { border: none; border-radius: 12px; box-shadow: 0 4px 20px rgba(0,0,0,0.1); }
        .table thead { background-color: #212529; color: white; position: sticky; top: 0; z-index: 10; }
        .badge-narcotic { background-color: #d63384; color: white; border-radius: 4px; padding: 2px 6px; font-size: 0.75rem; font-weight: bold; }
        .badge-common { background-color: #198754; color: white; border-radius: 4px; padding: 2px 6px; font-size: 0.75rem; }
        .ward-tag { background-color: #0d6efd; color: white; font-weight: bold; width: 45px; display: inline-block; text-align: center; border-radius: 4px; }
        .scroll-area { max-height: 75vh; overflow-y: auto; background: white; border-radius: 8px; border: 1px solid #ddd; }
        .btn-add { background: #0d6efd; color: white; font-weight: bold; }
    </style>
</head>
<body>

<nav class="navbar navbar-dark bg-dark mb-3 shadow">
    <div class="container-fluid px-4">
        <span class="navbar-brand">🏥 <b>HospitalOS v4.0</b> | Система управления отделением</span>
        <span class="text-white small">Всего пациентов: <b id="pCount">0</b></span>
    </div>
</nav>

<div class="container-fluid px-4">
    <div class="row">
        <div class="col-lg-3">
            <div class="card p-4 hospital-card mb-4">
                <h5 class="text-primary mb-3">Приемный покой</h5>
                <div class="mb-2">
                    <label class="form-label small mb-1">Фамилия</label>
                    <input type="text" id="newLName" class="form-control form-control-sm" placeholder="Напр: Петров">
                </div>
                <div class="mb-2">
                    <label class="form-label small mb-1">Имя</label>
                    <input type="text" id="newFName" class="form-control form-control-sm" placeholder="Напр: Иван">
                </div>
                <div class="mb-2">
                    <label class="form-label small mb-1">№ Палаты</label>
                    <input type="text" id="newWard" class="form-control form-control-sm" placeholder="101">
                </div>
                <div class="mb-3">
                    <label class="form-label small mb-1">Диагноз</label>
                    <textarea id="newDiag" class="form-control form-control-sm" rows="2"></textarea>
                </div>
                <button onclick="addNewPatient()" class="btn btn-add w-100 shadow-sm">Госпитализировать</button>
            </div>
        </div>

        <div class="col-lg-9">
            <div class="card p-3 hospital-card">
                <div class="table-responsive scroll-area">
                    <table class="table table-sm table-hover align-middle mb-0">
                        <thead>
                            <tr>
                                <th>Палата</th>
                                <th>Пациент</th>
                                <th>Диагноз</th>
                                <th>Назначения</th>
                                <th class="text-end">Действие</th>
                            </tr>
                        </thead>
                        <tbody id="patientTable"></tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
</div>

<div class="modal fade" id="medModal" tabindex="-1">
    <div class="modal-dialog modal-dialog-centered">
        <div class="modal-content hospital-card p-3">
            <div class="modal-header border-0 pb-0">
                <h6 class="modal-title">Лист назначений: <b id="modalPatientName" class="text-primary"></b></h6>
                <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
            </div>
            <div class="modal-body">
                <div class="row g-2">
                    <div class="col-md-6 border-end pe-3">
                        <label class="small fw-bold">Каталог (выбрать):</label>
                        <select id="catalogSelect" class="form-select form-select-sm mb-2" onchange="useCatalog()">
                            <option value="">-- Выберите из базы --</option>
                            <optgroup label="Обычные">
                                <option value="Аспирин|500мг|Обычное">Аспирин</option>
                                <option value="Анальгин|2мл|Обычное">Анальгин</option>
                                <option value="Дексаметазон|4мг|Обычное">Дексаметазон</option>
                                <option value="Цефтриаксон|1г|Обычное">Цефтриаксон</option>
                            </optgroup>
                            <optgroup label="Наркотические">
                                <option value="Морфин|10мг|Наркотическое">Морфин ⚠️</option>
                                <option value="Промедол|2%|Наркотическое">Промедол ⚠️</option>
                                <option value="Фентанил|0.05мг|Наркотическое">Фентанил ⚠️</option>
                            </optgroup>
                        </select>
                        <p class="text-muted" style="font-size: 10px;">* Выбор из каталога автоматически заполнит поля справа</p>
                    </div>
                    <div class="col-md-6 ps-3">
                        <label class="small fw-bold">Ручной ввод / Правка:</label>
                        <input type="text" id="medName" class="form-control form-control-sm mb-2" placeholder="Название">
                        <input type="text" id="medDose" class="form-control form-control-sm mb-2" placeholder="Доза (напр: 1 таблетка)">
                        <select id="medType" class="form-select form-select-sm">
                            <option value="Обычное">Обычное</option>
                            <option value="Наркотическое">Наркотическое</option>
                        </select>
                    </div>
                </div>
            </div>
            <div class="modal-footer border-0">
                <button onclick="savePrescription()" class="btn btn-success w-100 shadow">Подписать и назначить</button>
            </div>
        </div>
    </div>
</div>

<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
<script>
    let patients = [
        {fName: "Иван", lName: "Иванов", ward: "101", diag: "ОРВИ", meds: []},
        {fName: "Сергей", lName: "Петров", ward: "102", diag: "Пневмония", meds: [{name: "Цефтриаксон", dose: "1г", type: "Обычное"}]}
    ];
    let currentPatientIndex = null;

    function renderTable() {
        const table = document.getElementById('patientTable');
        table.innerHTML = "";
        document.getElementById('pCount').innerText = patients.length;

        patients.forEach((p, index) => {
            let medBadges = p.meds.map(m => 
                `<span class="${m.type === 'Наркотическое' ? 'badge-narcotic' : 'badge-common'} me-1 mb-1 d-inline-block">
                    ${m.name} [${m.dose}]
                </span>`
            ).join("");

            table.innerHTML += `
                <tr>
                    <td><span class="ward-tag">${p.ward}</span></td>
                    <td><b class="text-uppercase">${p.lName}</b> ${p.fName}</td>
                    <td><small>${p.diag}</small></td>
                    <td>${medBadges || '<i class="text-muted">Нет листов</i>'}</td>
                    <td class="text-end">
                        <button onclick="openMedModal(${index})" class="btn btn-sm btn-primary">💊</button>
                        <button onclick="deletePatient(${index})" class="btn btn-sm btn-outline-danger">✕</button>
                    </td>
                </tr>
            `;
        });
    }

    // ДОБАВЛЕНИЕ НОВОГО
    function addNewPatient() {
        const lName = document.getElementById('newLName').value;
        const fName = document.getElementById('newFName').value;
        const ward = document.getElementById('newWard').value;
        const diag = document.getElementById('newDiag').value;

        if (lName && fName && ward) {
            patients.unshift({ fName, lName, ward, diag, meds: [] }); // Добавляем в начало
            renderTable();
            // Чистим поля
            document.getElementById('newLName').value = "";
            document.getElementById('newFName').value = "";
            document.getElementById('newWard').value = "";
            document.getElementById('newDiag').value = "";
        } else {
            alert("Заполните Фамилию, Имя и Номер палаты!");
        }
    }

    // РАБОТА С ЛЕКАРСТВАМИ
    function openMedModal(index) {
        currentPatientIndex = index;
        document.getElementById('modalPatientName').innerText = patients[index].lName;
        document.getElementById('medName').value = "";
        document.getElementById('medDose').value = "";
        document.getElementById('catalogSelect').value = "";
        new bootstrap.Modal(document.getElementById('medModal')).show();
    }

    function useCatalog() {
        const val = document.getElementById('catalogSelect').value;
        if (val) {
            const [name, dose, type] = val.split('|');
            document.getElementById('medName').value = name;
            document.getElementById('medDose').value = dose;
            document.getElementById('medType').value = type;
        }
    }

    function savePrescription() {
        const name = document.getElementById('medName').value;
        const dose = document.getElementById('medDose').value;
        const type = document.getElementById('medType').value;
        if(name && dose) {
            patients[currentPatientIndex].meds.push({name, dose, type});
            renderTable();
            bootstrap.Modal.getInstance(document.getElementById('medModal')).hide();
        }
    }

    function deletePatient(index) {
        if(confirm("Выписать пациента?")) {
            patients.splice(index, 1);
            renderTable();
        }
    }

    renderTable();
</script>
</body>
</html>
