### Create the connection - the engine
---
```
from sqlalchemy import create_engine
engine = create_engine('sqlite:///:memory:', echo=True)
```

 - **create_engine** is the main argument passed by string URL with identify what engine will be used.
   - The engine is how the SQLAlchemy communicates with a database.
   - In this case SQLite where:
     - Use three slash where the two firsts is to indicate the path and the third is use to indicate the current path with base.
     - **:memory:** which is an indicator to the sqlite3 will be using an in-memory-only.
   - And using echo parameter will make the SQLAlchemy to log all SQL commands.

### Sessions
---
 - The ORM must be a session to make a meddle-ground between the objects and the engine that actually communicate with the database. So passing in **sessionmaker** the engine.
  
   ```
   from sqlalchemy.orm import sessionmaker
   session_create = sessionmaker(bind=engine)
   session = session_create()
   ```
    - So we use the session to talk with tables and make queries, but the engine will implemented things on db.

### Create tables
---
 - To create a table is use a base class with provide constructors[[1](#notes)].
 
   ```
   from sqlalchemy.ext.declarative import declarative_base
   base = declarative_base() 
   ```

 - Now we create a class **User** that inherits from our **base** declarative.
 - By use the declarative is required add two parameter:
   - ```__tablename__``` where is pass the class name(table).
   - At least one column which is a primary key.
 - By optional use in this case the ```__repr__``` that will be a string that will be returned when we see our user instance.

 ```
  from sqlalchemy import Column, Integer, String, nullable

  class User(Base):
      __tablename__ = 'users'

      id = Column(Integer, primary_key=True, nullable=False)
      name = Column(String)
      password = Column(String)

      def __repr__(self):
          return f'User {self.name}'
  ```
  - Now to actually create the the tables in the database is necessary use the declarative base and create engine.

    ```
    base.metadata.create_all(engine)
    ```

### Adding new records
---
 - Now to create nee record on db is just pass user in the class.

   ```
   user = User(name='John Snow', password='johnspassword')
   session.add(user)
   ```
 - But the command ```session.add()``` just register the transaction, but doesn't yet communicate with db them to the db until ```session.flush()``` is called.
 - ```session.flush()```  is responsible to communicate with db a series of changes (insert, update, delete) and the db maintains them pending until receive the commit command ```session.commit()``` so turn them permanent[[2](#notes)].

### Making queries
---
 - To make queries for one user in db is necessary use de session(define above) withe the query function and pass the class(table), and calling filter_by passing the username.

   ```
   query = session.query(User).filter_by(name='John')
   ```
 - Finally, pass a method to indicate what want to do with this query: count the number of records found ```.count()```, return all records found ```.all()```, return the first record ```.first()```...

   ```
   query.count()
   ```
 - Another way is to use the filter method, instead of the ```filter_by```, which has a slightly different syntax:

   ```
   session.query(User).filter(User.name=='John').first()
   ```
 - With filter method, can also look for strings similar to what you have:
 - 
   ```
   session.query(User).filter(User.name.like('%John%')).first()
   ```
 
 - With the simple use from ```@classmethod``` is possible make short functions to do this queries, pass session and an attribute:

   ```
   class User(Base):
   ...

   @classmethod
   def find_by_name(cls, session, name):
     return session.query(cls).filter_by(name=name).all()
   ```
  - So is just use:

    ```
    Product.find_by_name(session, 'John')
    ```

### PS
---
 - Creating new tables tables after initial create_all
   - After have the class created is just use the follow command pass the class(table) name.

     ```
     class_name(table).__table__.create(engine)
     ```
 - Creating a Foreign Key relationship[[3](#notes)]

## NOTES
---
 > 1. [declarative_base()](https://stackoverflow.com/questions/15175339/sqlalchemy-what-is-declarative-base#:~:text=51,in%20main.py.)
 > 2. [permanent session](https://stackoverflow.com/questions/4201455/sqlalchemy-whats-the-difference-between-flush-and-commit/4202016#:~:text=A%20Session%20object,4%20%5B%3CFoo(%27A%27)%3E%5D)
 > 3. [Foreign key](https://leportella.com/sqlalchemy-tutorial/#:~:text=__table__.create(engine)-,Creating%20a%20Foreign%20Key%20relationship,-Imagine%20that%20you)
 > - [Blog reference](https://leportella.com/sqlalchemy-tutorial/)<br>
 > - [SQLAlchemy tutorial](https://docs.sqlalchemy.org/en/14/orm/tutorial.html)