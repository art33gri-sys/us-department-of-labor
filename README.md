import os
from flask import Flask, render_template_string, request, redirect, url_for, session, flash
from flask_sqlalchemy import SQLAlchemy
from werkzeug.security import generate_password_hash, check_password_hash

app = Flask(__name__)
app.secret_key = 'super_secret_hospital_key'
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///hospital_v2.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

db = SQLAlchemy(app)

# --- МОДЕЛИ ДАННЫХ ---
class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(50), unique=True, nullable=False)
    password = db.Column(db.String(200), nullable=False)
    full_name = db.Column(db.String(100))

class Patient(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    first_name = db.Column(db.String(50))
    last_name = db.Column(db.String(50))
    ward = db.Column(db.String(10))
    diagnosis = db.Column(db.String(200))
    prescriptions = db.Column(db.Text, default="")
    notes = db.Column(db.Text, default="")

# --- HTML ШАБЛОНЫ (Встроенные) ---
LOGIN_HTML = """
<!DOCTYPE html>
<html>
<head>
    <title>Вход в МИС</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body class="bg-dark d-flex align-items-center" style="height: 100vh;">
    <div class="container card p-4 shadow" style="max-width: 400px;">
        <h3 class="text-center mb-4">Вход для персонала</h3>
        <form method="POST">
            <input type="text" name="username" class="form-control mb-3" placeholder="Логин (admin)" required>
            <input type="password" name="password" class="form-control mb-3" placeholder="Пароль (1234)" required>
            <button class="btn btn-primary w-100">Войти в систему</button>
        </form>
        {% with messages = get_flashed_messages() %}
            {% if messages %}<div class="alert alert-danger mt-3">{{ messages[0] }}</div>{% endif %}
        {% endwith %}
    </div>
</body>
</html>
"""

DASHBOARD_HTML = """
<!DOCTYPE html>
<html>
<head>
    <title>Панель управления</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body class="bg-light">
    <nav class="navbar navbar-dark bg-primary shadow-sm mb-4">
        <div class="container">
            <span class="navbar-brand">Hospital <b>OS</b> | {{ session['user_name'] }}</span>
            <a href="{{ url_for('logout') }}" class="btn btn-outline-light btn-sm">Выход</a>
        </div>
    </nav>

    <div class="container">
        <div class="row">
            <div class="col-md-4">
                <div class="card shadow-sm p-3 mb-4">
                    <h5>Прием пациента</h5>
                    <form action="{{ url_for('add_patient') }}" method="POST">
                        <div class="mb-2"><input type="text" name="f_name" class="form-control" placeholder="Имя" required></div>
                        <div class="mb-2"><input type="text" name="l_name" class="form-control" placeholder="Фамилия" required></div>
                        <div class="mb-2"><input type="text" name="ward" class="form-control" placeholder="№ Палаты" required></div>
                        <div class="mb-2"><textarea name="diagnosis" class="form-control" placeholder="Диагноз"></textarea></div>
                        <button class="btn btn-success w-100">Добавить в базу</button>
                    </form>
                </div>
            </div>

            <div class="col-md-8">
                <div class="card shadow-sm p-3">
                    <h5>Текущие пациенты</h5>
                    <table class="table table-hover mt-3">
                        <thead class="table-secondary">
                            <tr>
                                <th>Палата</th>
                                <th>ФИО</th>
                                <th>Диагноз</th>
                                <th>Назначения</th>
                            </tr>
                        </thead>
                        <tbody>
                            {% for p in patients %}
                            <tr>
                                <td><span class="badge bg-info text-dark">№ {{ p.ward }}</span></td>
                                <td><b>{{ p.last_name }}</b> {{ p.first_name }}</td>
                                <td>{{ p.diagnosis }}</td>
                                <td>
                                    <form action="{{ url_for('update_meds', p_id=p.id) }}" method="POST" class="d-flex">
                                        <input type="text" name="meds" class="form-control form-control-sm" value="{{ p.prescriptions }}" placeholder="Лекарство...">
                                        <button class="btn btn-sm btn-primary ms-1">OK</button>
                                    </form>
                                </td>
                            </tr>
                            {% endfor %}
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
</body>
</html>
"""

# --- МАРШРУТЫ (ЛОГИКА) ---

@app.route('/', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        user = User.query.filter_by(username=request.form['username']).first()
        if user and check_password_hash(user.password, request.form['password']):
            session['user_id'] = user.id
            session['user_name'] = user.full_name
            return redirect(url_for('dashboard'))
        flash('Неверный логин или пароль!')
    return render_template_string(LOGIN_HTML)

@app.route('/dashboard')
def dashboard():
    if 'user_id' not in session:
        return redirect(url_for('login'))
    patients = Patient.query.all()
    return render_template_string(DASHBOARD_HTML, patients=patients)

@app.route('/add_patient', methods=['POST'])
def add_patient():
    if 'user_id' in session:
        new_p = Patient(
            first_name=request.form['f_name'],
            last_name=request.form['l_name'],
            ward=request.form['ward'],
            diagnosis=request.form['diagnosis']
        )
        db.session.add(new_p)
        db.session.commit()
    return redirect(url_for('dashboard'))

@app.route('/update_meds/<int:p_id>', methods=['POST'])
def update_meds(p_id):
    if 'user_id' in session:
        patient = Patient.query.get(p_id)
        patient.prescriptions = request.form['meds']
        db.session.commit()
    return redirect(url_for('dashboard'))

@app.route('/logout')
def logout():
    session.clear()
    return redirect(url_for('login'))

# --- ЗАПУСК ПРИЛОЖЕНИЯ ---

if __name__ == '__main__':
    with app.app_context():
        db.create_all()
        # Создаем администратора по умолчанию
        if not User.query.filter_by(username='admin').first():
            admin = User(
                username='admin',
                password=generate_password_hash('1234'),
                full_name='Д-р Александр Иванов'
            )
            db.session.add(admin)
            db.session.commit()
            print("База создана. Данные для входа: admin / 1234")
            
    app.run(debug=True, port=5000)
