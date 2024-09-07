# app.py
from flask import Flask, render_template, request, redirect, url_for
import datetime

app = Flask(__name__)

# Dados simulados para agendamentos
appointments = []

# Perguntas e respostas do chatbot
faq = {
    "horário de funcionamento": "Nossas agências funcionam de segunda a sexta, das 9h às 18h.",
    "rastrear pacote": "Para rastrear seu pacote, utilize o código de rastreamento disponível no nosso site.",
    "endereços disponíveis": "Você pode encontrar os endereços das nossas agências no site oficial dos Correios.",
    "serviços oferecidos": "Oferecemos serviços de envio de cartas, pacotes, encomendas, além de serviços financeiros."
}

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/chatbot', methods=['GET', 'POST'])
def chatbot():
    response = ""
    if request.method == 'POST':
        user_input = request.form['message'].lower()
        response = faq.get(user_input, "Desculpe, não entendi sua pergunta. Por favor, tente novamente.")
    return render_template('chatbot.html', response=response)

@app.route('/schedule', methods=['GET', 'POST'])
def schedule():
    if request.method == 'POST':
        name = request.form['name']
        email = request.form['email']
        date_str = request.form['date']
        time_str = request.form['time']
        try:
            appointment_date = datetime.datetime.strptime(date_str, '%Y-%m-%d').date()
            appointment_time = datetime.datetime.strptime(time_str, '%H:%M').time()
            appointments.append({
                'name': name,
                'email': email,
                'date': appointment_date,
                'time': appointment_time
            })
            return redirect(url_for('success'))
        except ValueError:
            return "Data ou horário inválido. Por favor, tente novamente."
    return render_template('schedule.html')

@app.route('/success')
def success():
    return "Agendamento realizado com sucesso! Você receberá um e-mail de confirmação em breve."

@app.route('/view_appointments')
def view_appointments():
    return render_template('view_appointments.html', appointments=appointments)

if __name__ == '__main__':
    app.run(debug=True)
