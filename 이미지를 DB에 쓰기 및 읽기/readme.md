#### DB에 이미지를 저장하는 방식      

이미지는 binary data 이므로 DB에 저장하기 위해선 text data로 바꿔야 하고 이를 위해선 이미지를 base64로 encoding 해주는 과정이 필요하다.    
base64 encoding을 하게 되면 전송해야 될 데이터의 양이 약 33% 늘어나게 되는 단점이 발생하지만 ASCII encoding보다 데이터를 더 안전하게 전달할 수 있기에 사용한다.    

***        

#### DB 준비

이미지를 DB에 저장할 때는 BLOB 타입으로 저장한다.    

```MySQL
CREATE TABLE test(Id INT PROMARY KEY AUTO_INCREMENT, Data MEDIUMBLOB);
```

BLOB 타입은 indexing을 할 수 없으므로 indexing을 하려면 별도의 column이 존재해야 한다.    
TINYBLOB 은 255Bytes / BLOB 은 64KB / MEDIUMBLOB 은 16MB / LONGBLOB 은 4GB 까지의 이미지를 저장할 수 있다. 

#### DB에 이미지 쓰기       

```python
import pymysql.cursors

connection = pymysql.connect(host='i4d109.p.ssafy.io',port=3306,user="user",password="pwd",db="A", cursorclass=pymysql.cursors.DictCursor)

with connection.cursor() as cursor:

	# image 테이블 전체 조회
	sql = "SELECT * FROM images"
	cursor.execute(sql)
	results = cursor.fetchall()
    
	# image 테이블에 추가
	sql = "INSERT INTO images VALUES (%s,%s,%s)"
	values = ((값 1),(값 2),(값 3))
	cursor.execute(sql, values)
	connection.commit()
    
	# image 테이블 수정
	sql = "UPDATE images SET state = 'T' WHERE id = %s"
	values = ((값 1))
	cursor.execute(sql, values)
	connection.commit()
    
connection.close()
```
