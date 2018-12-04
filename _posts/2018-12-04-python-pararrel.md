---
title: "[Python] 파이썬 멀티 쓰레드(thread)와 멀티 프로세스(process)"
categories: develop python
---



# 파이썬 멀티 쓰레드(thread)와 멀티 프로세스(process)

파이썬은 인터프리터 언어로서 기본적으로 싱글 쓰레드에서 순차적으로 동작한다. 따라서 병렬처리를 하기 위해서는 별도의 모듈을 사용하여 구현해야 한다. 이 글에서는 threding 모듈을 이용한 쓰레드 구현과 multiprocessing 모듈을 이용한 프로세스 구현을 소개한다.



## threding 모듈로 멀티쓰레드 구현하기

파이썬에서 멀티 쓰레드를 구현하는 방법은 **threding** 모듈(High level)을 사용하거나 **thread**(Low level) 모듈을 사용하는 것이며, 현재 thread 모듈은 deprecated 되어 threading 모듈을 사용하는 것을 권장한다.

먼저 0부터 100,000,000 까지의 합을 구하는 계산 프로그램을 하나의 쓰레드로 동작하게 만들어보자.

```python
from threading import Thread

def work(id, start, end, result):
    total = 0
    for i in range(start, end):
        total += i
    result.append(total)
    return

if __name__ == "__main__":
    START, END = 0, 100000000
    result = list()
    th1 = Thread(target=work, args=(1, START, END, result))
    
    th1.start()
    th1.join()

print(f"Result: {sum(result)}")
```

쓰레드는 threading 모듈의 Thread 함수로 쓰레드 객체를 받아 사용한다. target은 쓰레드가 실행할 함수, args는 그 함수의 인자들을 의미한다. start 함수로 쓰레드를 시작하고 join 함수로 쓰레드가 끝날 때까지 기다린다. 위의 코드의 실행 시간은 약 **4.6초**가 걸렸다.



다음으로 쓰레드를 추가해서 병렬도 동작하는 코드를 만들어보자.

```python
if __name__ == "__main__":
    START, END = 0, 100000000
    result = list()
    th1 = Thread(target=work, args=(1, START, END//2, result))
    th2 = Thread(target=work, args=(2, END//2, END, result))
    
    th1.start()
    th2.start()
    th1.join()
    th2.join()
```

th2 를 추가했고, 쓰레드에서 실행되는 함수에 들어가는 인자를 절반씩 나누어 입력하여 따로 계산하도록 했다. 이 코드를 실행하면 하나의 프로세스에서 동작하지만 여러 cpu를 가지고 있다면 쓰레드가 적절히 분산되어 병렬 처리를 할 것이다. 계산 결과는 위와 같았고 실행 시간은 **4.6초**가 걸렸다.



응? 4.6초? 이상하다. 이전에 하나의 쓰레드로 동작시킨 것과 별반 다르지 않다.

이는 파이썬의 GIL 정책 때문이다.



## GIL(Global Interpreter Lock)

언어에서 자원을 보호하기 위해 락(Lock) 정책을 사용하고 그 방법 또한 다양하다. 파이썬에서는 **하나의 프로세스** 안에 **모든 자원**의 **락(Lock)**을 글로벌(Global)하게 관리함으로써 한번에 하나의 쓰레드만 자원을 컨트롤하여 동작하도록 한다.

위의 코드에서 result 라는 자원을 공유하는 두 개의 쓰레드를 동시에 실행시키지만, 결국 GIL 때문에 한번에 하나의 쓰레드만 계산을 실행하여 실행 시간이 비슷한 것이다.

GIL 덕분에 자원 관리(예를 들어 가비지 컬렉팅)를 더 쉽게 구현할 수 있었지만, 지금처럼 멀티 코어가 당연한 시대에서는 조금 아쉬운 것이 사실이다. 그렇다고 파이썬의 쓰레드가 쓸모 없는 것은 아니다. GIL이 적용되는 것은 **cpu 동작**에서이고 쓰레드가 cpu 동작을 마치고 **I/O 작업**을 실행하는 동안에는 다른 쓰레드가 cpu 동작을 **동시**에 실행할 수 있다. 따라서 cpu 동작이 많지 않고 I/O동작이 더 많은 프로그램에서는 멀티 쓰레드만으로 성능적으로 큰 효과를 얻을 수 있다.



## Multiprocessing 모듈로 멀티 프로세스 구현하기

이러한 상황에서 계산을 병렬로 처리하는데 도움을 주는 것이 바로 **multiprocessing** 모듈이다. multiprocessing 모듈은 쓰레드 대신 프로세스를 만들어 병렬로 동작한다. 위의 계산 프로그램을 멀티 프로세스로 구현한 코드는 다음과 같다.

```python
from multiprocessing import Process, Queue

def work(id, start, end, result):
    total = 0
    for i in range(start, end):
        total += i
    result.put(total)
    return

if __name__ == "__main__":
    START, END = 0, 100000000
    result = Queue()
    th1 = Process(target=work, args=(1, START, END//2, result))
    th2 = Process(target=work, args=(2, END//2, END, result))
    
    th1.start()
    th2.start()
    th1.join()
    th2.join()

    result.put('STOP')
    total = 0
    while True:
        tmp = result.get()
        if tmp == 'STOP':
            break
        else:
            total += tmp
    print(f"Result: {total}")
```

multiprocessing 모듈의 가장 큰 **장점**은 threding 모듈과 구현 방식이 거의 같아서 기존에 쓰레드 방식으로 구현한 코드를 **쉽게** 이식할 수 있다는 점이다. 위의 코드에서 변경된 것은 Thread 함수가 아닌 Process 함수에서 객체를 받아 사용하는 것과 result로 Queue 객체를 사용한 것뿐이다. 해당 코드를 실행하면 실행시간이 약 **2.6초**로 현저하게 감소한 것을 확인할 수 있다.

프로세스는 각자가 고유한 메모리 영역을 가지기 때문에 쓰레드에 비하면 메모리 사용이 늘어난다는 단점이 있지만, 이 방식을 통해 싱글 머신 아키텍처로부터 여러 머신을 사용하는 분산 애플리케이션으로 쉽게 전환할 수 있다.

각각의 프로세스가 자신만의 메모리 공간을 사용하기 때문에 프로세스간 **데이터 교환**을 위해 multiprocessing.Queue 객체를 사용해야 한다. multiprocessing 모듈에서는 Queue 이외에도 Pipe 객체를 지원하여 데이터 교환을 돕는다.



## Thread vs Process

결론적으로 말하자면, 파이썬에서 **병렬처리**를 구현하는 방식은 두가지로 **멀티 쓰레드**를 사용하거나 **멀티 프로세스**를 사용하는 것이다. **쓰레드**는 가볍지만 GIL로 인해 계산 처리를 하는 작업은 한번에 하나의 쓰레드에서만 작동하여 cpu 작업이 적고 **I/O 작업**이 많은 병렬 처리 프로그램에서 효과를 볼 수 있다.

**프로세스**는 각자가 고유한 메모리 영역을 가지기 때문에 **더 많은 메모리**를 필요로 하지만, 각각 프로세스에서 **병렬**로 cpu 작업을 할 수 있고 이를 이용해 여러 머신에서 동작하는 **분산 처리** 프로그래밍도 구현할 수 있다.

각자의 장단점을 고려하여 자신의 프로그램에 잘 맞는 방식을 사용하자.



## 참고

[예제로 배우는 Python 프로그래밍 - 쓰레드](http://pythonstudy.xyz/python/article/24-%EC%93%B0%EB%A0%88%EB%93%9C-Thread )

[파이썬으로 클라우드가 하고 싶어요 - 하용호](https://www.slideshare.net/kthcorp/h32011c6pythonandcloud-111205023210phpapp02 )

[파이썬 분산 컴퓨팅](http://acornpub.co.kr/book/distributed-computing  )

