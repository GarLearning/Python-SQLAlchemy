### Simple example
---

```
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:////tmp/test.db'
db = SQLAlchemy(app)


class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)

    def __repr__(self):
        return '<User %r>' % self.username
```
 - Model is the declarative class on flask_sqlalchemy.
 - The line (app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:////tmp/test.db') define path from database by app.config if don't declare this the default is "sqlite:///:memory:" memory db.
### How execute
---

```
from application import db
db.create_all()
```
### Create users
---

```
from application import User
admin = User(username='admin', email='admin@example.com')
guest = User(username='guest', email='guest@example.com')
```
### Commit to database
---

```
db.session.add(admin)
db.session.add(guest)
db.session.commit()
```
### Make queries
---

```
User.query.all()
[<User u'admin'>, <User u'guest'>]
User.query.filter_by(username='admin').first()
<User u'admin'>
```
### NOTES
 > - Just simples because the explanation is on [basics](basics.md) folder.
 > - [Flask minimal example content](https://flask-sqlalchemy.palletsprojects.com/en/2.x/quickstart/#a-minimal-application).
 > - [Select, Insert, Delete(do queries)](https://flask-sqlalchemy.palletsprojects.com/en/2.x/queries/)