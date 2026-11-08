from flask import Flask, request, jsonify, send_from_directory
from flask_cors import CORS
import json
import os
from datetime import datetime

app = Flask(__name__, static_folder='.')
CORS(app)

DATA_FILE = 'suggestions.json'
ADMIN_PASSWORD = "admin1234"  # 원하는 비밀번호로 변경하세요

def load_data():
    if not os.path.exists(DATA_FILE):
        return []
    try:
        with open(DATA_FILE, 'r', encoding='utf-8') as f:
            return json.load(f)
    except:
        return []

def save_data(data):
    with open(DATA_FILE, 'w', encoding='utf-8') as f:
        json.dump(data, f, ensure_ascii=False, indent=2)

@app.route('/')
def index():
    return send_from_directory('.', 'index.html')

@app.route('/api/submit', methods=['POST'])
def submit():
    data = request.json
    if not data or not data.get('problem'):
        return jsonify({"error": "문제 내용은 필수입니다"}), 400

    item = {
        "id": int(datetime.now().timestamp() * 1000),
        "grade": data.get('grade', ''),
        "facility": data.get('facility', ''),
        "type": data.get('type', ''),
        "problem": data.get('problem', ''),
        "suggestion": data.get('suggestion', ''),
        "anonymous": data.get('anonymous', True),
        "name": data.get('name', '익명'),
        "date": datetime.now().strftime('%Y-%m-%d %H:%M')
    }

    suggestions = load_data()
    suggestions.insert(0, item)
    save_data(suggestions)

    return jsonify({"success": True, "message": "건의가 접수되었습니다"})

@app.route('/api/list', methods=['GET'])
def list_suggestions():
    password = request.args.get('password', '')
    if password != ADMIN_PASSWORD:
        return jsonify({"error": "비밀번호가 틀렸습니다"}), 403

    return jsonify(load_data())

if __name__ == '__main__':
    print("서버 시작: http://localhost:5000")
    print(f"관리자 비밀번호: {ADMIN_PASSWORD}")
    app.run(host='0.0.0.0', port=5000, debug=True)
