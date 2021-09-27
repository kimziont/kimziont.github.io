---
title:  "[Intermediate] 파이썬의 코루틴(Coroutine)"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/python.png

categories:
  - intermediate
tags:
  - Python
last_modified_at: 2021-09-26
---

# 코루틴(Coroutine)

## 1. 제너레이터  
앞에서 배운 제너레이터는 일방적으로 주기(yield)만 했다면, 이번에 배울 코루틴 포스트에서는 제너레이터를 이용해 데이터를 서로 지속적으로 주고 받을 수 있게 만들어보겠습니다.  

## 2. 코루틴
그동안 앞에서 배웠던 방식들은 모두 `서브 루틴`이라고 하며 여기서 `루틴은 프로그램이 실행되는 순서` 정도로 생각하면 되겠습니다.  

### 1. 서브루틴
서브루틴은 반복되는 특정 기능을 별도로 묶어 이름을 붙여 놓은 것으로 메인루틴을 보조하는 역할을 합니다. 보통 함수나 메소드 등으로 불리며 사용됩니다. 서브루틴은 별도의 메모리에 해당 기능을 모아 놓고 있어, 서브루틴이 호출될 때마다 저장된 메모리로 이동했다가 return 을 통해 원래 호출자의 위치로 돌아오게 됩니다. 호출할 때마다 매번 같은 위치로 이동하기 때문에 여러번 사용될 수 있으므로 효율적이라 할 수 있겠지요.

### 2. 코루틴
코루틴도 서브루틴처럼 기능들을 별도의 공간에 모아 놓고 있다는 점에서는 동일합니다. 차이점이라 할 수 있는 것은, 서브루틴의 경우에는 메인루틴에서 특정 서브루틴의 공간으로 이동한 후에 리턴에 의해 호출자로 돌아와 다시 프로세스를 진행하는데 반해 코루틴의 경우에는 루틴을 진행하는 중간에 멈추어서 특정 위치로 돌아갔다가 다시 원래 위치로 돌아와 나머지 루틴을 수행할 수 있습니다. 또 한가지 차이점은 서브루틴은 진입점과 반환점이 단 하나밖에 없어 메인루틴에 종속적이지만, 코루틴은 진입지점이 여러개이기 때문에 메인루틴에 종속적이지 않아 대등하게 데이터를 주고 받을 수 있다는 특징이 있습니다.

## 3. 코루틴 관련 메소드

### 1) send()
- `receive = yield produced` 형태로 많이 사용된다
- 먼저 next()함수로 yield 구문까지 실행한다
- send() 메소드에서 받은 인자 값을 코루틴의 receive에 전달해준다 
- send() 메소드를 호출하려면 항상 next()함수를 먼저 호출해야 한다
- 그렇지 않으면 `TypeError` 발생한다.

### 2) close()

### 3) throw()

## 4. 예시  

```python
def coroutine():
    try:
        while True:
            received = yield "coroutine is producing value"
            if received:
                print(f"I got {received} from sender\n")

    except GeneratorExit:
        return


c = coroutine()

print(c.__next__())
c.send(5)

print(c.__next__())
c.send(1534)
--------------------------------------------------
coroutine is producing value
I got 5 from sender

coroutine is producing value
I got 1534 from sender
```