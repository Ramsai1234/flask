### Flask simple register and login Task 
## app.py

```
from flask import Flask, render_template, request, redirect, url_for, session
import pandas as pd
import os

## wsgi
app = Flask(__name__)
app.secret_key = 'mysecretkey'

EXCEL_FILE = 'users.xlsx'

# Create users.xlsx if not exists
if not os.path.exists(EXCEL_FILE):
    df = pd.DataFrame(columns=['username', 'password'])
    df.to_excel(EXCEL_FILE, index=False)


def read_users():
    return pd.read_excel(EXCEL_FILE)


def add_user(username, password):
    df = read_users()
    if username in df['username'].values:
        return False  # user already exists
    new_user = pd.DataFrame({'username': [username], 'password': [password]})
    df = pd.concat([df, new_user], ignore_index=True)
    df.to_excel(EXCEL_FILE, index=False)
    return True


def check_credentials(username, password):
    df = read_users()
    user_row = df[(df['username'] == username) & (df['password'] == password)]
    return not user_row.empty


@app.route('/')
def home():
    return redirect(url_for('login'))


@app.route('/register', methods=['GET', 'POST'])
def register():
    if request.method == 'POST':
        username = request.form['username']
        password = request.form['password']
        if add_user(username, password):
            return redirect(url_for('login'))
        else:
            return render_template('register.html', error="Username already exists!")
    return render_template('register.html')


@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        username = request.form['username']
        password = request.form['password']

        if check_credentials(username, password):
            session['username'] = username
            return redirect(url_for('welcome'))
        else:
            return render_template('login.html', error='Invalid username or password.')
    return render_template('login.html')


@app.route('/welcome')
def welcome():
    if 'username' in session:
        return render_template('welcome.html', username=session['username'])
    else:
        return redirect(url_for('login'))


@app.route('/logout')
def logout():
    session.pop('username', None)
    return redirect(url_for('login'))


if __name__ == '__main__':
    app.run(debug=True)


```
# register output

<img width="1462" height="823" alt="image" src="https://github.com/user-attachments/assets/723129b8-1a53-467d-bd78-6d94c0509c10" />
# login Output

<img width="1252" height="827" alt="image" src="https://github.com/user-attachments/assets/8a41ec3b-e596-44f8-8051-64f19cdc16b4" />
# Final Output

<img width="1471" height="705" alt="image" src="https://github.com/user-attachments/assets/890602d4-7b44-4d65-8a9e-f36fffcd9eeb" />
