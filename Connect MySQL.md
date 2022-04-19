# Connect MySQL

- MySQL과 파이썬을 연결해 준다.

```python
!pip install pymysql
```

```python
HOST = "ec2-13-209-40-91.ap-northeast-2.compute.amazonaws.com"

PORT = 3306
```

```python
import pymysql

word_db = pymysql.connect(
	user="유저 이름",
	passwd="비밀번호",
	host = HOST,
	port = PORT,
	db = "world" # 데이터베이스 이름
)

word_db
```

```python
# 데이터 한 개 한 개를 가르키는 말 (딕셔너리 형태로 저장)

cursor = word_db.cursor(pymysql.cursors.DictCursor)
cursor
```

```python
sql = "SELECT * FROM city;"

# 결과물을 갖고만 있는 상태
cursor.execute(sql)
```

```python
# 커서가 가르키고 있는 쿼리 실행 결과물을 딕셔너리 형식으로 다 가져옴

result = cursor.fetchall()
```

```python
import pandas as pd

df_city = pd.DataFrame(result)
df_city.head()
```



## 국가별 인구수가 제일 많은 도시를 시각화로 나타내기

```python
df2 = df_city.groupby("CountryCode")[["Population"]].max()
df2.head()
```

```python
import seaborn as sns
import matplotlib.pyplot as plt
```

```python
df2_copy = df2.sort_values(by="Population", ascending=False)
df2_copy1 = df2_copy.head(10)
```

```python
sns.barplot(x=df2_copy1.index, y="Population", data=df2_copy1)
plt.show()
```



## 함수 여러 개 사용

```python
df3 = df_city.groupby("CountryCode")[["Population"]].agg(["max","min"])
df3.head()
```

