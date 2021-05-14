#### 설치모듈

Anaconda Prompt로 아래 명령어를 통해 해당 모듈을 먼저 설치해야 한다.

```
pip install schedule
```



***



#### 코드

```Python
import schedule

def run():
  print("done \n")
  
schedule.every(1).seconds.do(run)

while True:
  schedule.run_pending()
  time.sleep(1)
```


***



#### 실행 결과

1초마다 run 함수가 실행된다.    