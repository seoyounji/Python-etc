#### DB에 이미지를 저장하는 방식      

이미지는 binary data 이므로 DB에 저장하기 위해선 text data로 바꿔야 하고 이를 위해선 이미지를 base64로 encoding 해주는 과정이 필요하다.    
base64 encoding을 하게 되면 전송해야 될 데이터의 양이 약 33% 늘어나게 되는 단점이 발생하지만 ASCII encoding보다 데이터를 더 안전하게 전달할 수 있기에 사용한다.    

***        

#### DB 준비

이미지를 DB에 저장할 때는 BLOB 타입으로 저장한다.    

```MySQL
CREATE TABLE test(Id INT PROMARY KEY AUTO_INCREMENT, Data MEDIUMBLOB, Type VARCHAR(50));
```

BLOB 타입은 indexing을 할 수 없으므로 indexing을 하려면 별도의 column이 존재해야 한다.    
TINYBLOB 은 255Bytes / BLOB 은 64KB / MEDIUMBLOB 은 16MB / LONGBLOB 은 4GB 까지의 이미지를 저장할 수 있다. 

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
	
		sql = "INSERT INTO test(Data, Type) VALUES($s, %s)"
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
