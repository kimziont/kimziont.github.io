---
title:  "[Intermediate] 파이썬의 실행 속도"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/python.png

categories:
  - intermediate
tags:
  - Python
last_modified_at: 2021-03-25
---



## GIL(Global Interpreter Lock)

파이썬으로 쓰레드를 공부하다 보면 GIL에 대한 이야기가 심심치 않게 나온다. 파이썬이 발전되는 과정에서 하드웨어 역시 발전되어 왔다. 어느 순간 CPU는 클럭을 높이는 쪽에서 코어를 늘리는 쪽을 택했다. 이런 변화는 프로그래밍 언어에도 큰 영향을 미쳤다. 왜냐하면 1코어 시대에는 CPU가 1개의 코어만 있는게 당연했지만, 현재는 다중 코어 CPU를 사용할 수 있도록 언어적인 차원에서도 지원을 해야하기 때문이다. 그런데 파이썬(Cpython)은 GIL이라고 해서 인터프리터에 락을 거는 방식으로 다중 코어를 동시실행하지 못하도록 했다.  

### 파이썬 인터프리터와 성능  

파이썬은 인터프리터를 통해서 코드를 해석한다. 이 말은 파이썬 코드가 실행되기 위해서는 파이썬 인터프리터라는 프로그램이 먼저 메모리에 로드되어야 한다는 뜻이다. 마치 JAVA언어의 JVM이 자바 바이트 코드를 번연해서 CPU에 넘겨주는 것과 비슷한 개념이다.  
따라서 파이썬의 실행 속도는 매우 느리다.  

### 파이썬의 메모리 관리  

파이썬은 객체의 생성과 소멸을 reference count를 통해서 관리한다. refcount란 객체가 가지고 있는 속성으로 자신을 몇군데에서 참조하고 있는지에 대한 속성이다. 그리고 인터프리터는 refcount를 검사해 해당 객체의 메모리를 회수할지를 결정한다.  

### 파이썬에 GIL이 있는 이유  

파이썬에서는 예를 들어, A라는 스크립트를 실행하면 2개의 쓰레드가 생긴다고 해보자. 2개의 쓰레드를 1개의 코어 시스템에서 돌리면 동시에 돌아가는 것처럼 보여도 사실은 CPU가 두 쓰레드를 번갈아 가면서 처리한다.  
그런데 멀티코어 CPU라면 어떻게 될까? C와 같은 언어에서는 쓰레드가 여러 코어에서 동시에 돌아간다. 하지만 파이썬에서는 GIL 때문에  언제나 하나의 코어가 하나의 쓰레드만 실행한다. 다시 말해서 특정 시점에서 인터프리터를 사용하는 쓰레드는 언제나 1개라는 것이다. 이렇게 인터프리터에 뮤텍스(mutex)락을 걸어서 GIL(Global Interpreter Lock)이라는 이름이 붙여졌다.  

그러면 파이썬은 왜 GIL을 채택했을까?  

인터프리터는 객체에 참조가 생기거나 해제될 때마다 refcount를 증감하게 된다. 쓰레드 간에 공유하는 객체가 있다면 객체를 참조하는 연산(=)은 경쟁 상태(race condition)가 되고 모든 객체에 대해서 참조가 증가 또는 감소될 때 lock을 걸어야 한다는 의미가 된다. 즉 모든 객체가 크리티컬 섹션이 된다.  
모든 것이 객체로 이루어진 파이썬에서는 이렇게 객체마다 lock을 걸어야 하면 매우 비효율적 일 수 밖에 없다.  
그래서 파이썬은 인터프리터를 락을 걸어버렸다. 쓰레드가 몇 개든 CPU 코어에 관계없이 인터프리터를 사용하는 쓰레드는 오직 1개로 만들었다.  

### GIL의 의의  

보통 멀티 코어를 필요로하는 프로그램은 특수한 경우다. 또한 multiprocessing모듈을 통해서 멀티 프로세서 프로그래밍으로 여러 코어를 사용할 수 있다. 그렇기 때문에 GIL이 그렇게 엄청난 문제를 야기한다고는 할 수 없다.

https://dgkim5360.tistory.com/entry/understanding-the-global-interpreter-lock-of-cpython  

https://ssungkang.tistory.com/entry/python-GIL-Global-interpreter-Lock%EC%9D%80-%EB%AC%B4%EC%97%87%EC%9D%BC%EA%B9%8C  

https://m.blog.naver.com/alice_k106/221566619995  

https://jeongukjae.github.io/posts/python-GIL/  

https://thrillfighter.tistory.com/621  

https://box0830.tistory.com/333