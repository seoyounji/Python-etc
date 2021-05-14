#### DB에 이미지를 저장하는 방식      

앞서 말하지만 DB에 이미지 그 자체를 저장하는건 그닥 좋은 생각이 아니다........!               

디비에 raw 이미지 데이터를 저장하는 건 굉장히 비효율적이라서 현재 대부분의 회사들은 자체 이미지 서버를 가지고 있고 각 이미지의 경로를 디비에 저장하는 방식을 사용한다.                

하지만 토이 프로젝트를 할 때는 이미지 서버를 구축하는게 워낙 귀찮기도 하고ㅎㅎ.... 적은 수의 이미지만 저장할거라면 상관없지 않을까 하는 마음으로 일단 알아만 보자.              

이미지는 binary data 이므로 DB에 저장하기 위해선 text data로 바꿔야 하고 이를 위해선 이미지를 base64로 encoding 해주는 과정이 필요하다.    
base64 encoding을 하게 되면 전송해야 될 데이터의 양이 약 33% 늘어나게 되는 단점이 발생하지만 ASCII encoding보다 데이터를 더 안전하게 전달할 수 있기에 사용한다.    

***

#### DB 준비

이미지를 DB에 저장할 때는 BLOB 타입 or TEXT 타입으로 저장할 수 있다. 난 옛날부터 BLOB 타입만 주로 사용했는데 알고보니 TEXT 타입이 프론트엔드에서 이미지로 나타낼 때 훨씬 편하다는 걸 알게 돼서 그 뒤론 TEXT 타입만 사용하고 있다.              

```MySQL
CREATE TABLE test(Id INT PROMARY KEY AUTO_INCREMENT, Data TEXT, Type VARCHAR(50));
```

BLOB 타입이나 TEXT 타입은 indexing을 할 수 없으므로 indexing을 하려면 별도의 column이 존재해야 한다.               

***

#### 로컬 이미지 DB에 저장하기

```MySQL
import pymysql.cursors
import base64
import pathlib

connection = pymysql.connect(host='127.0.0.1',port=3306,user="user",password="pwd",db="Image", cursorclass=pymysql.cursors.DictCursor)

with connection.cursor() as cursor:
	filepath = '/image.png'
	path = pathlib.Path(filepath)
	
	with open(filepath, 'rb') as img :
		base64_string = base64.b64encode(img.read())
	
		sql = "INSERT INTO test(Data, Type) VALUES(%s, %s)"
		values = (base64_string, path.suffix)
		
		cursor.execute(sql, values)
		connection.commit()

connection.close()
```

***

#### DB에 있는 이미지 로컬에 저장하기       

```MySQL
import pymysql.cursors
import base64
from PIL import Image
from io image BytesIO

connection = pymysql.connect(host='127.0.0.1',port=3306,user="user",password="pwd",db="Image", cursorclass=pymysql.cursors.DictCursor)

with connection.cursor() as cursor:

	# test 테이블 전체 조회
	sql = "SELECT * FROM images"
	cursor.execute(sql)
	results = cursor.fetchall()
    
	for result in results:
		filename = "save%d" % result['Id']
		img = Image.open(BytesIO(base64.b64decode(result['Data'])))
		if(result['Type'] == 'png'):
			img.save(filename+".png")
			
			# 만약 png 파일을 jpg로 저장하고 싶다면?
			# img = img.convert("RGB")
			# img.save(filename+".jpg")
    		else:
			img.save(filename+".jpg")
connection.close()
```
