---
layout  : wiki
title   : 
summary : 
date    : 2020-05-18 21:14:37 +0900
updated : 2020-05-21 15:56:38 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

{% raw %}

## Hello, World

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
    return 'Hello world!'
```

```python
$ FLASK_APP=app.py flask run
```

```python
$ pip install flask-sqlalchemy
$ pip inttall Flask-WTF # from 관리
```

```python
$ python app.py
$ sqlite3 db.splite
$ .tables
$ .schema
```

### app.py

```python
import os
from flask import Flask
from flask import request
from flask import redirect
from flask import render_template
from models import db

from models import Fcuser

app = Flask(__name__)

@app.route('/register', methods=['GET', 'POST'])
def register():
    if request.method == 'POST':
        userid = request.form.get('userid')
        username = request.form.get('username')
        password = request.form.get('password')
        re_password = request.form.get('re-password')

        if (userid and username and password and re_password) and password == re_password:
            fcuser = Fcuser()
            fcuser.userid = userid
            fcuser.username = username
            fcuser.password = password

            db.session.add(fcuser)
            db.session.commit()

            return redirect('/')

    return render_template('register.html')

@app.route('/')
def hello():
    return render_template('hello.html')

if __name__ == "__main__":
    basedir = os.path.abspath(os.path.dirname(__file__)) # 현재파일주소를 절대경로로
    dbfile = os.path.join(basedir, 'db.sqlite')

    app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///' + dbfile # 데이터베이스 지정
    app.config['SQLALCHEMY_COMMIT_ON_TEARDOWN'] = True # 반영의미
    app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False # 수정사항 추적

    db.init_app(app) # 위에 없는 앱 설정값을 모두 초기화
    db.app = app # 앱을 디비 안으로 명시화
    db.create_all()
    app.run(host='127.0.0.1', port=5000, debug=True)

```

### models.py


```python
from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy()

class Fcuser(db.Model):
    __tablename__ = 'fcuser'
    id = db.Column(db.Integer, primary_key=True)
    password = db.Column(db.String(64))
    userid = db.Column(db.String(32))
    username = db.Column(db.String(8))
```

### templates/register.html

```html
<html>

<head>
  <meta charset='utf-8' />
  <meta name='viewport' content='width=device-width, initial-scale=1, shrink-to-fit=no' />

  <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css"
    integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
  <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js"
    integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous">
  </script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js"
    integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous">
  </script>
  <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js"
    integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous">
  </script>
</head>

<body>
  <div class="container">
    <div class="row mt-5">
      <h1>회원가입</h1>
    </div>
    <div class="row mt-5">
      <div class="col-12">
        <form method="POST">
          <div class="form-group">
            <label for="userid">아이디</label>
            <input type="text" class="form-control" id="userid" placeholder="아이디" name="userid" />
          </div>
          <div class="form-group">
            <label for="username">사용자 이름</label>
            <input type="text" class="form-control" id="username" placeholder="사용자 이름" name="username" />
          </div>
          <div class="form-group">
            <label for="password">비밀번호</label>
            <input type="password" class="form-control" id="password" placeholder="비밀번호" name="password" />
          </div>
          <div class="form-group">
            <label for="re-password">비밀번호 확인</label>
            <input type="password" class="form-control" id="re-password" placeholder="비밀번호 확인" name="re-password" />
          </div>
          <button type="submit" class="btn btn-primary">등록</button>
        </form>
      </div>
    </div>
  </div>
</body>

</html>
```

## flask-todoapp

### app.py

```python
import os
from flask import Flask
from flask import request, redirect, render_template, session
from models import db, Fcuser, Todo
from forms import RegisterForm, LoginForm
from api_v1 import api as api_v1

app = Flask(__name__)
app.register_blueprint(api_v1, url_prefix='/api/v1')

@app.route('/', methods=['GET'])
def home():
    userid = session.get('userid', None)
    todos = []
    if userid:
        fcuser = Fcuser.query.filter_by(userid=userid).first()
        todos = Todo.query.filter_by(fcuser_id=fcuser.id)

    return render_template('home.html', userid=userid, todos=todos)

@app.route('/login', methods=['GET', 'POST'])
def login():
    form = LoginForm()
    if form.validate_on_submit():
        session['userid'] = form.data.get('userid')

        return redirect('/')

    return render_template('login.html', form=form)

@app.route('/logout', methods=['GET'])
def logout():
    session.pop('userid', None)
    return redirect('/')

@app.route('/register', methods=['GET', 'POST'])
def register():
    form = RegisterForm()
    if form.validate_on_submit():
        fcuser = Fcuser()
        fcuser.userid = form.data.get('userid')
        fcuser.password = form.data.get('password')

        db.session.add(fcuser)
        db.session.commit()

        return redirect('/login')

    return render_template('register.html', form=form)

basedir = os.path.abspath(os.path.dirname(__file__))
dbfile = os.path.join(basedir, 'db.sqlite')

app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///' + dbfile
app.config['SQLALCHEMY_COMMIT_ON_TEARDOWN'] = True
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
app.config['SECRET_KEY'] = 'jqiowejrojzxcovnklqnweiorjqwoijroi'

csrf = CSRFProtect()
csrf.init_app(app)
db.init_app(app)
db.app = app
db.create_all()

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000, debug=True)
```

### models.py

```python
from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy()

class Todo(db.Model):
    __tablename__ = 'todo'

    id = db.Column(db.Integer, primary_key=True)
    fcuser_id = db.Column(db.Integer, db.ForeignKey('fcuser.id'), nullable=False)
    title = db.Column(db.String(256))
    status = db.Column(db.Integer)
    due = db.Column(db.String(64))
    tstamp = db.Column(db.DateTime, server_default=db.func.now())

    @property
    def serialize(self):
        return {
            'id': self.id,
            'title': self.title,
            'fcuser': self.fcuser.userid,
            'tstamp': self.tstamp
        }


class Fcuser(db.Model):
    __tablename__ = 'fcuser'

    id = db.Column(db.Integer, primary_key=True)
    userid = db.Column(db.String(32))
    password = db.Column(db.String(128))
    todos = db.relationship('Todo', backref='fcuser', lazy=True)

```

### forms.py

```python
from models import Fcuser
from flask_wtf import FlaskForm
from wtforms import StringField, PasswordField
from wtforms.validators import DataRequired, EqualTo # 커맨드 누르고 클릭하면 소스 까볼 수 있다 -> UserPassword의 구조는 DataRequired에서 참고

class RegisterForm(FlaskForm):
    userid = StringField('userid', validators=[DataRequired()])
    password = PasswordField('password', validators=[DataRequired(), EqualTo('repassword')])
    repassword = PasswordField('repassword', validators=[DataRequired()])

class LoginForm(FlaskForm):

    class UserPassword(object):
        def __init__(self, message=None):
            self.message = message
            
        def __call__(self, form, field):
            userid = form['userid'].data
            password = field.data

            fcuser = Fcuser.query.filter_by(userid=userid).first() # 첫번째값
            if fcuser.password != password:
                raise ValueError('Wrong password')

    userid = StringField('userid', validators=[DataRequired()])
    password = PasswordField('password', validators=[DataRequired(), UserPassword()])
```

### templates/register.html

- static 파일 그냥 static 폴더 만들어서 저장
- url_for() 함수 사용하면 static 폴더에 없어도 파일 추적 가능

```html
<html>

<head>
  <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css"
    integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous" />
  <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js"
    integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous">
  </script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js"
    integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous">
  </script>
  <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js"
    integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous">
  </script>
</head>

<body>
  <div class="container">
    <div class="row mt-5">
      <h1>회원가입</h1>
    </div>
    <div class="row mt-5">
      <div class="col-12">
        <form method="POST" action="/register">
          {{ form.csrf_token }}
          <div class="form-group">
            {{ form.userid.label("아이디") }}
            {{ form.userid(class="form-control", placeholder="아이디") }}
          </div>
          <div class="form-group">
            {{ form.password.label("비밀번호") }}
            {{ form.password(class="form-control", placeholder="비밀번호") }}
          </div>
          <div class="form-group">
            {{ form.repassword.label("비밀번호 확인") }}
            {{ form.repassword(class="form-control", placeholder="비밀번호 확인") }}
          </div>
          <button type="submit" class="btn btn-primary">등록</button>
        </form>
      </div>
    </div>
  </div>

</body>

</html>
```

### templates/login.html

```html
<html>

<head>
  <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css"
    integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous" />
  <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js"
    integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous">
  </script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js"
    integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous">
  </script>
  <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js"
    integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous">
  </script>
  # <link rel="stylesheet" href="{{ url_for('static', filename='css/style.css') }}"> static폴더에 없어도 파일 경로 자동으로 찾아주는 함수 url_for
</head>

<body>
  <div class="container">
    <div class="row mt-5">
      <h1>로그인</h1>
    </div>
    <div class="row mt-5">
      <div class="col-12">
        <form method="POST" action="/login">
          {{ form.csrf_token }}
          <div class="form-group">
            {{ form.userid.label("아이디") }}
            {{ form.userid(class="form-control", placeholder="아이디") }}
          </div>
          <div class="form-group">
            {{ form.password.label("비밀번호") }}
            {% if form.password.errors %} # password 내 errors를 담는 그릇이 있다
            {{ form.password.errors.0 }} # errors의 0번째
            {% endif %}
            {{ form.password(class="form-control", placeholder="비밀번호") }}
          </div>
          <button type="submit" class="btn btn-primary">로그인</button>
        </form>
      </div>
    </div>
  </div>

</body>

</html>
```

### templates/home.html

```python
<html>

<head>
  <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css"
    integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous" />

  <script src="https://code.jquery.com/jquery-3.4.1.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js"
    integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous">
  </script>
  <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js"
    integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous">
  </script>

  <link rel="stylesheet"
    href="https://cdnjs.cloudflare.com/ajax/libs/bootstrap-datepicker/1.9.0/css/bootstrap-datepicker.min.css" />
  <script src="https://cdnjs.cloudflare.com/ajax/libs/bootstrap-datepicker/1.9.0/js/bootstrap-datepicker.min.js">
  </script>
  <script>
    $(document).ready(function () {
      $("#date").datepicker({});
    });

    function createTodo() {
      $.ajax({
        url: '/api/v1/todos',
        contentType: 'application/json',
        method: 'POST',
        data: JSON.stringify({
          title: $("#title").val(),
          due: $("#date").val()
        })
      }).done(function (res) {
        $("#title").val('');
        $("#date").val('');

        window.location = '/';
      });
    }

    function doneTodo(todo_id) {
      $.ajax({
        url: '/api/v1/todos/done',
        contentType: 'application/json',
        method: 'PUT',
        data: JSON.stringify({
          todo_id: todo_id
        })
      }).done(function (res) {
        window.location = '/';
      });
    }
  </script>
</head>

<body>
  <div class="container">
    <div class="row mt-5">
      <h1>Home</h1>
    </div>
    <div class="row mt-5">
      <div class="col-12">
        <ul class="nav">
          {% if userid %}
          <li class="nav-item">
            <a class="nav-link" href="/logout">로그아웃</a>
          </li>
          <li class="nav-item">
            <a class="nav-link disabled" href="#" tabindex="-1" aria-disabled="true">{{ userid }}</a>
          </li>
          {% else %}
          <li class="nav-item">
            <a class="nav-link" href="/login">로그인</a>
          </li>
          <li class="nav-item">
            <a class="nav-link" href="/register">회원가입</a>
          </li>
          {% endif %}
        </ul>
      </div>
    </div>
    <div class="row mt-5">
      <h3>할일 생성</h3>
      <div class="col-12">
        <!-- 할일 제목 -->
        <input type="text" class="form-control" id="title" placeholder="할일 제목" />
      </div>
      <div class="col-12 col-sm-7 mt-2">
        <!-- 날짜 -->
        <input type="text" class="form-control" id="date" placeholder="기한" />
      </div>
      <div class="col-12 col-sm-5 mt-2">
        <!-- 확인 -->
        <button type="button" class="btn btn-primary" onclick="createTodo();">생성</button>
      </div>
    </div>
    <div class="row mt-5">
      <h3>할일 목록</h3>
      <div class="col-12">
        <table class="table">
          <thead>
            <tr>
              <th scope="col">#</th>
              <th scope="col">할일제목</th>
              <th scope="col">기한</th>
              <th scope="col">완료처리</th>
            </tr>
          </thead>
          <tbody>
            {% for todo in todos %}
            {% if todo.status %}
            <tr>
              <th scope="row"><del>{{ todo.id }}</del></th>
              <td><del>{{ todo.title }}</del></td>
              <td><del>{{ todo.due }}</del></td>
              <td>완료</td>
            </tr>
            {% else %}
            <tr>
              <th scope="row">{{ todo.id }}</th>
              <td>{{ todo.title }}</td>
              <td>{{ todo.due }}</td>
              <td><button type="button" class="btn btn-secondary" onclick="doneTodo({{ todo.id }});">완료</button></td>
            </tr>
            {% endif %}
            {% endfor %}
          </tbody>
        </table>
      </div>
    </div>
  </div>
</body>

</html>
```

### api_v1/todo.py

```python
from flask import jsonify
from flask import request
from flask import Blueprint
from flask import session
from models import Todo, db, Fcuser
import datetime
import requests
from . import api

def send_slack(msg):
    res = requests.post('https://hooks.slack.com/services/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', json={
        'text': msg
    }, headers={'Content-Type': 'application/json'})


@api.route('/todos/done', methods=['PUT'])
def todos_done():
    userid = session.get('userid', 1)
    if not userid:
        return jsonify(), 401

    data = request.get_json()
    todo_id = data.get('todo_id')

    todo = Todo.query.filter_by(id=todo_id).first()
    fcuser = Fcuser.query.filter_by(userid=userid).first()

    if todo.fcuser_id != fcuser.id:
        return jsonify(), 400

    todo.status = 1

    db.session.commit()
    send_slack('TODO가 완료되었습니다\n사용자: %s\n할일 제목:%s'%(fcuser.userid, todo.title))

    return jsonify()

@api.route('/todos', methods=['GET', 'POST', 'DELETE'])
def todos():
    userid = session.get('userid', None)
    if not userid:
        return jsonify(), 401

    if request.method == 'POST':
        data = request.get_json()
        todo = Todo()
        todo.title = data.get('title')

        fcuser = Fcuser.query.filter_by(userid=userid).first()
        todo.fcuser_id = fcuser.id

        todo.due = data.get('due')
        todo.status = 0
        
        db.session.add(todo)
        db.session.commit()

        send_slack('TODO가 생성되었습니다\n사용자: %s\n할일 제목:%s\n기한:%s'%(fcuser.userid, todo.title, todo.due))

        return jsonify(), 201
    elif request.method == 'GET':
        todos = Todo.query.filter_by(fcuser_id=userid, status=0) 
        return jsonify([t.serialize for t in todos])
    elif request.method == 'DELETE':
        data = request.get_json()
        todo_id = data.get('todo_id')

        todo = Todo.query.filter_by(id=todo_id).first()
        
        db.session.delete(todo)
        db.session.commit()

        return jsonify(), 203

    return jsonify(data)


@api.route('/slack/todos', methods=['POST'])
def slack_todos():
    res = request.form['text'].split(' ')
    cmd, *args = res

    ret_msg = ''
    if cmd == 'create':
        todo_user_id = args[0]
        todo_name = args[1]
        todo_due = args[2]

        fcuser = Fcuser.query.filter_by(userid=todo_user_id).first()
        
        todo = Todo()
        todo.fcuser_id = fcuser.id
        todo.title = todo_name
        todo.due = todo_due
        todo.status = 0
        
        db.session.add(todo)
        db.session.commit()
        ret_msg = 'Todo가 생성되었습니다'

        send_slack('[%s] "%s" 할일을 만들었습니다.'%(str(datetime.datetime.now()), todo_name)) # 사용자 정보, 할일 제목, 기한

    elif cmd == 'list':
        todo_user_id = args[0]
        fcuser = Fcuser.query.filter_by(userid=todo_user_id).first()

        todos = Todo.query.filter_by(fcuser_id=fcuser.id)
        for todo in todos:
            ret_msg += '%d. %s (~ %s, %s)\n'%(todo.id, todo.title, todo.due, ('미완료', '완료')[todo.status])

    elif cmd == 'done':
        todo_id = args[0]
        todo = Todo.query.filter_by(id=todo_id).first()
        
        todo.status = 1
        db.session.commit()
        ret_msg = 'Todo가 완료 처리되었습니다'
        
    elif cmd == 'undo':
        todo_id = args[0]
        todo = Todo.query.filter_by(id=todo_id).first()
        
        todo.status = 0
        db.session.commit()       
        ret_msg = 'Todo가 미완료 처리되었습니다'

    return ret_msg
```

## PythonAnywhere 배포

- [pythonanywhere](https://www.pythonanywhere.com/)

- 파일 압축해서 업로드(가상환경파일 제외)
- Open Bash console here

```python
$ unzip.project.zip -d project

$ virtualenv --python=python3.8 falsk_fastcampus
$ source flask_fastcampus/bin/activate
$ pip install flask flask-wtf flask-sqlalchemy
$ exit
```

- Web - add a new web app
- Code -> 디렉토리 설정
- Virtualenv -> 디렉토리 설정
- WSGI
    - HELLO_WORLD 주석 설정
    - FLASK 주석 해제
        - 폴더 위치 수정
    
```python
from app import app as application # noqa
```



## Link

- [파이썬 웹 개발](https://www.fastcampus.co.kr/dev_online_pyweb)

{% endraw %}
