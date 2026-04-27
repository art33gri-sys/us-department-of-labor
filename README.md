from datetime import datetime

class Patient:
    def __init__(self, patient_id, first_name, last_name, ailment):
        self.id = patient_id
        self.first_name = first_name
        self.last_name = last_name
        self.ailment = ailment
        self.history = []  # Заметки и история болезни
        self.prescriptions = [] # Список лекарств
        self.assigned_doctor = None

    def add_note(self, note):
        timestamp = datetime.now().strftime("%Y-%m-%d %H:%M")
        self.history.append(f"[{timestamp}] {note}")

    def add_prescription(self, medicine):
        self.prescriptions.append(medicine)

    def __str__(self):
        return f"Пациент: {self.first_name} {self.last_name} | Диагноз: {self.ailment}"


class Doctor:
    def __init__(self, doctor_id, name, specialization):
        self.id = doctor_id
        self.name = name
        self.specialization = specialization
        self.patients = []

    def assign_patient(self, patient):
        self.patients.append(patient)
        patient.assigned_doctor = self.name

    def treat_patient(self, patient, note, medicine=None):
        if patient in self.patients:
            patient.add_note(f"Врач {self.name}: {note}")
            if medicine:
                patient.add_prescription(medicine)
        else:
            print(f"Ошибка: Пациент {patient.last_name} не закреплен за врачом {self.name}")


class Ward:
    def __init__(self, number, capacity):
        self.number = number
        self.capacity = capacity
        self.occupants = []

    def add_patient(self, patient):
        if len(self.occupants) < self.capacity:
            self.occupants.append(patient)
            return True
        return False

    def list_patients(self):
        return [f"{p.first_name} {p.last_name}" for p in self.occupants]


class HospitalSystem:
    def __init__(self):
        self.wards = {}
        self.doctors = {}
        self.all_patients = {}

    def register_doctor(self, doctor):
        self.doctors[doctor.id] = doctor

    def admit_patient(self, patient, ward_number):
        if ward_number in self.wards and self.wards[ward_number].add_patient(patient):
            self.all_patients[patient.id] = patient
            print(f"Пациент {patient.last_name} успешно госпитализирован в палату №{ward_number}.")
        else:
            print(f"Нет мест в палате №{ward_number} или она не существует.")

# --- ДЕМОНСТРАЦИЯ РАБОТЫ ---

# 1. Инициализация системы
my_hospital = HospitalSystem()
my_hospital.wards[101] = Ward(101, capacity=2)

# 2. Создаем персонал
dr_house = Doctor(1, "Грегори Хаус", "Диагност")
my_hospital.register_doctor(dr_house)

# 3. Прием пациента
patient_smith = Patient(1001, "Иван", "Петров", "Волчанка (вероятно)")
my_hospital.admit_patient(patient_smith, 101)

# 4. Назначение врача и лечение
dr_house.assign_patient(patient_smith)
dr_house.treat_patient(patient_smith, "Назначен МРТ головного мозга", "Викодин 5мг")
dr_house.treat_patient(patient_smith, "Состояние стабильное", "Витамин C")

# 5. Вывод данных
print("\n--- Отчет по пациенту ---")
print(patient_smith)
print(f"Лечащий врач: {patient_smith.assigned_doctor}")
print("История болезни:")
for record in patient_smith.history:
    print(record)
print(f"Назначенные лекарства: {', '.join(patient_smith.prescriptions)}")

print(f"\nЗаполненность палаты 101: {my_hospital.wards[101].list_patients()}")
