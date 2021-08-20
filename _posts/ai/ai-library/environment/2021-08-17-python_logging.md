---
title:  "[AI를 위한 환경설정] Pyhon logging"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/deep_learning.jpg

categories:
  - Dev_environment
tags:
  - AI
last_modified_at: 2021-08-17
---


## Python logging

```python
import logging

# log를 찍으면 logger의 이름이 해당 파일명으로 나온다
logger = logging.getLogger(__name__)

# 모든 레벨의 로그를 Handler들에게 전달해야 합니다.
logger.setLevel(logging.DEBUG) 

formatter = logging.Formatter('%(asctime)s:%(module)s:%(levelname)s:%(message)s', '%Y-%m-%d %H:%M:%S')

# INFO 레벨 이상의 로그를 콘솔에 출력하는 Handler
console_handler = logging.StreamHandler()
console_handler.setLevel(logging.INFO)
console_handler.setFormatter(formatter)
logger.addHandler(console_handler)

# DEBUG 레벨 이상의 로그를 `debug.log`에 출력하는 Handler
file_debug_handler = logging.FileHandler('debug.log')
file_debug_handler.setLevel(logging.DEBUG)
file_debug_handler.setFormatter(formatter)
logger.addHandler(file_debug_handler)

# ERROR 레벨 이상의 로그를 `error.log`에 출력하는 Handler
file_error_handler = logging.FileHandler('error.log')
file_error_handler.setLevel(logging.ERROR)
file_error_handler.setFormatter(formatter)
logger.addHandler(file_error_handler)

for i in range(30):
    if i % 2 == 0:
        # debug는 가장 낮은 레벨의 로그 -> debug뿐 아니라 error도 로그로 남는다
        logger.debug(f"debug occured because {i} is even number")

    else:
        # error는 critical 다음으로 높은 레벨의 로그 -> error만 로그로 남는다
        logger.error(f'error occured because {i} is odd number')

# 콘솔에 찍히는 로그는 info 레벨 이상의 로그 -> error만 로그
```

![](/assets/images/log_1.png){: width="100%"}  

![](/assets/images/log_2.png){: width="100%"}  

![](/assets/images/log_3.png){: width="100%"}  

[[ㅎㅎㅋ 블로그 참조]](https://hwangheek.github.io/2019/python-logging/)