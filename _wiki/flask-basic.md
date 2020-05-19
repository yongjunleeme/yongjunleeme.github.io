---
layout  : wiki
title   : 
summary : 
date    : 2020-05-18 21:14:37 +0900
updated : 2020-05-19 18:40:44 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

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
