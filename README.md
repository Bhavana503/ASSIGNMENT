from flask import Flask, request, jsonify

app = Flask(_name_)

# Sample data (replace with a database in a production environment)
doctors = [
    {
        'id': 1,
        'name': 'Dr. Smith',
        'available_days': ['Monday', 'Wednesday', 'Friday'],
        'max_patients': 10
    },
    {
        'id': 2,
        'name': 'Dr. Johnson',
        'available_days': ['Tuesday', 'Thursday'],
        'max_patients': 8
    }
]

appointments = []

# Endpoint to get a list of doctors
@app.route('/api/doctors', methods=['GET'])
def get_doctors():
    return jsonify(doctors)

# Endpoint to get details of a specific doctor by ID
@app.route('/api/doctors/<int:doctor_id>', methods=['GET'])
def get_doctor(doctor_id):
    doctor = next((d for d in doctors if d['id'] == doctor_id), None)
    if doctor:
        return jsonify(doctor)
    return jsonify({'message': 'Doctor not found'}), 404

# Endpoint to book an appointment
@app.route('/api/appointments', methods=['POST'])
def book_appointment():
    data = request.get_json()
    doctor_id = data.get('doctor_id')
    patient_name = data.get('patient_name')

    doctor = next((d for d in doctors if d['id'] == doctor_id), None)

    if not doctor:
        return jsonify({'message': 'Doctor not found'}), 404

    # Check if the doctor is available on the current day (for simplicity)
    current_day = data.get('current_day')
    if current_day not in doctor['available_days']:
        return jsonify({'message': 'Doctor is not available today'}), 400

    # Check if the doctor's appointments are full
    appointment_count = sum(1 for app in appointments if app['doctor_id'] == doctor_id)
    if appointment_count >= doctor['max_patients']:
        return jsonify({'message': 'Doctor is fully booked'}), 400

    appointment = {
        'doctor_id': doctor_id,
        'patient_name': patient_name
    }
    appointments.append(appointment)

    return jsonify({'message': 'Appointment booked successfully'}), 201

if _name_ == '_main_':
    app.run(debug=True)
