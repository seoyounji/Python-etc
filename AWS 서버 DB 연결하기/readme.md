#### AWS 환경      

AWS EC2 서버명(DNS) : i4d109.p.ssafy.io     
접속 id : user        
접속 password : pwd      
DB명 : A         
***        

#### 코드       

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
