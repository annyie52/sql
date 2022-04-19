

# MySQLclient

```python
!pip install mysqlclient
```

```python
import MySQLdb
```

```python
HOST = "ec2-13-209-40-91.ap-northeast-2.compute.amazonaws.com"
```

```python
db = MySQLdb.connet(
	HOST,
	"유저 이름",
	"비밀 번호",
	"world" #db 이름
)
```

```python
QUERY= """ 
  SHOW
  TABLES;
"""

cursor = db.cursor()
cursor.execute(QUERY)
cursor.fetchall()
```

```python
import pandas as pd

QUERY = """
  SELECT *
  FROM country;
"""

df_country = pd.read_sql(QUERY, db)
df_country.head()
```



# SQLAlchemy

```python
from sqlalchemy import *
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker
```

```python
HOST = "ec2-13-209-40-91.ap-northeast-2.compute.amazonaws.com"
DB_USER="유저 이름"
DB_PASSWD="비밀번호"
DB_NAME = "test"
conn = f"mysql://{DB_USER}:{DB_PASSWD}@{HOST}/{DB_NAME}?charset=utf8"

engine = create_engine(conn, encoding = "utf-8")
```

```python
# 엔진 정보 생성

engine = create_engine(conn)
engine
```

```python
# Base: 기본적으로 테이블을 다루기 위한 여러 내용들이 들어 있다.
# CRUD 메소드가 구현되어 있다.

Base = declarative_base()

# Base 클래스 상속
class User(Base):
    
    __tablename__ = "user2" # 매핑될 테이블의 이름 필요
    
    # 컬럼 정의
    user_id = Column(Integer, primary key=True) # AUTO_INCREMENT 성질을 가지고 있음
    name = Column(String(20))
    email = Column(String(30))
    age = Column(Integer)
    rdate = Column(DateTime) # Default now()
    
    def __init__(self, name, email, age):
        self.name = name
        self.email = email
        self.age = age
        
     # 파이썬의 매직 메소드
    def __repr__(self):
        return "<User {}, {}, {}, {}>".format(self.name, self.email, self.age, self.rdate)
```

```python
# engine에 연결된 데이터베이스(test db)에 테이블 생성
Base_metadata.create_all(engine)
```

```python
# 데이터베이스 세션 연결: connection (연결 정보를 이용하여 실제로 연결)

Session = Sessionmaker(engine)

sess = Session()
sess
```



## INSERT

- `session` 객체에 add(), add_all()을 사용하면 된다.

```python
user = User("annyie_colab", "katherine0325@daum.net", 24)
user
```

```python
sess.add(user)
sess.commit()
```

```python
users = [User("mark", "mark@gmail.com", 25),
User("kenne", "kenne@gmail.com", 30)]

Session = Sessionmaker(engine)
sess = Session()
sess.add_all(users)
sess.commit()
```



## SELECT

```python
Session = Sessionmaker(engine)
sess = Session()
                   # FROM 절
result = sess.query(User).all()
list(result)
```

```python
result = sess.query(User.name, User.age).all()
list(result)
```



## WHERE절: `filter`

```python
# 비교 연산자 활용
result = sess.query(User).filter(User.age > 25)
list(result)
```

```python
result = sess.query(User).filter(User.email.like("%gmail%"))
list(result)
```

```python
result = sess.query(User).filter(User.age.in_([24, 25]))
list(result)
```

```python
result = sess.query(User).filter(or_(User.name == "kenne", User.age == 30))
list(result)
```

```python
# order by
result = sess.query(User).order_by(User.age.desc())
list(result)
```

```python
# group by
from sqlalchemy import func
sess.query(User.age, func.count(User.age)).group_by(User.age).all()
```



## UPDATE

```python
data = sess.query(User).filter(User.name == "anne").one()

data.age = 9999

# Primary key가 겹치는 객체를 add하면 update된다.
sess.add(data)
sess.commit()
```



## DELETE

```python
sess.query(User).filter(User.age == 30).delete()
sess.commit()
```



# SQLAlchemy, Pandas 연동

```python
import pandas as pd
df_idol = pd.read_csv("korean-idol.csv")

conn = f"mysql://{DB_USER}:{DB_PASSWD}@{HOST}/{DB_NAME}?charset=utf8"

engine = create_engine(conn, encoding="utf-8")

df_idol.to_sql(name="kor_idol", con=engine, if_exists='replace')
```

