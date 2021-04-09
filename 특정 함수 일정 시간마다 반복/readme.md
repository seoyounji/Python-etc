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
