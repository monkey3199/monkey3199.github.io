---
title: "[Python] Python CLI 를 위한 라이브러리 'Click'"
categories: python library
---

# Python CLI 를 위한 라이브러리 'Click'

## Click?

오늘은 파이썬으로 간단하게 **CLI(Command Line Interface) 도구**를 만들 수 있게 도와주는 라이브러리인 **Click** 에 대해 알아보자. CLI 도구를 만들 때 가장 필요한 것은 바로 **옵션**이나 **인자**를 받아서 처리하는 것이다. 하지만 그것들을 직접 받아 파싱하고 처리하는 것이 은근히 귀찮다. Click 은 바로 이러한 귀차니즘을 **해결**하기 위해 **다양한 기능**들을 제공한다. 

## 간단한 예제

다음으로 간단한 예제를 통해서 어떻게 동작하는지 알아보자. 지금부터 나오는 예제들은 모두 Click의 [공식사이트](https://click.palletsprojects.com/en/7.x/)를 참고했으며 python 3.6에서 테스트했다. 먼저 pip 를 이용해서 click 을 설치한다.

```
$ pip install click
```

설치를 완료했으면 아래의 코드로 샘플 프로그램을 만들어보자.

```python  
import click

@click.command()
@click.option('--count', default=1, help='Number of greetings.')
@click.option('--name', prompt='Your name',
              help='The person to greet.')
def hello(count, name):
    """Simple program that greets NAME for a total of COUNT times."""
    for x in range(count):
        click.echo('Hello %s!' % name)

if __name__ == '__main__':
    hello()
```

위의 파이썬 스크립트를 실행해보자. 실행할 때 @click.option 으로 지정해준대로 —count 옵션을 인자로 넣어서 실행한다.

```bash
$ python click_sample.py --count=3
Your name: nathan
Hello nathan!
Hello nathan!
Hello nathan!
```

실행한 뒤에 'Your name:' 이 프롬프트로 출력되어 두번째 옵션인 name을 입력받는다. 이후에는 처음에 입력받은 count 값만큼 'Hello nathan' 이 출력되는 것을 볼 수 있다. 개인적으로 Click 라이브러리에서 가장 좋아하는 기능은 바로 **Help 페이지**를 **자동**으로 만들어준다는 것이다. 위의 코드처럼 옵션에 대한 설명을 미리 입력해두면 그것을 기반으로 다음과 같이 —help 옵션을 제공한다.

```bash
$ python click_sample.py --help
Usage: click_sample.py [OPTIONS]

  Simple program that greets NAME for a total of COUNT times.

Options:
  --count INTEGER  Number of greetings.
  --name TEXT      The person to greet.
  --help           Show this message and exit.
```

## Quickstart

이제 본격적인 여러 예제를 통해 더 자세히 알아보자.

### @click.command

Click 은  **데코레이터**를 통해 커맨드를 정의하는 것을 기본으로 이뤄져있다. 이게 무슨 말이냐 하면, 특정 함수를 만들어서 그 함수 위에 @를 붙인 일종의 **예약어**들을 표시하는 것으로 해당 함수가 적절하게 실행하도록 동작한다. 아직 이해를 하지 못했다면 코드를 보면 쉽게 이해할 수 있다.

```python
import click

@click.command()
def hello():
    click.echo('Hello World!')
    
if __name__ == '__main__':
    hello()
```

위처럼 hello 함수위에 @click.command() 라는 데코레이터를 붙여놓음으로써, 해당 함수를 커맨드 함수로 지정하는 것이다. 그리고 main 부분에서 hello 함수를 실행하도록 하고 코드를 실행하면 hello 함수에 정의된대로 'Hello World!' 를 출력한다.

```bash
$ python click_sample.py
Hello World!
```

물론 @click.command() 데코레이터를 붙이지 않아도 마찬자지로 문장을 출력한다. 이는 간단한 예제일 뿐이며, 이 커맨드 함수에 여러 인자를 정의하면(데코레이터를 이용해서) 코드를 실행할 때 인자를 넣는 것들이 커맨드 함수로 전달된다. 이는 뒤에서 더 자세하게 알아보도록 하자.

### Nesting Commands - 중첩 커맨드

Click 에서는 여러 커맨드들을 **복합적**으로 사용할 수 있다. 커맨드를 그룹 타입의 커맨드에 붙이는 것으로 중첩 커맨드를 만들 수 있는 것이다.

```python
@click.group()
def cli():
    pass

@click.command()
def initdb():
    click.echo('Initialized the database')

@click.command()
def dropdb():
    click.echo('Dropped the database')

cli.add_command(initdb)
cli.add_command(dropdb)
```

위 코드를 실행하면 다음과 같다.

```bash
$ python click_sample.py initdb
Initialized the database
$ python click_sample.py dropdb
Dropped the database
```

위에서 확인한대로 initdb 와 dropdb 커맨드를 cli 커맨드에 포함시켜 하나의 스크립트로 여러 커맨드를 실행할 수 있다. cli.add_command(initdb) 와 같이 함수를 실행시켜서 커맨드를 포함시킬 수도 있지만, 더 간단하게 데코레이터로 명시하는 방법도 있다.

```python
@click.group()
def cli():
    pass

@cli.command()
def initdb():
    click.echo('Initialized the database')

@cli.command()
def dropdb():
    click.echo('Dropped the database')
```

위처럼 데코레이터로 @cli.command() 라고 명세하면 cli 커맨드에 자동으로 포함된다.

### 인자 추가하기

인자를 추가할 때는 **option()** 과 **argument()** 데코레이터를 사용한다. 두가지가 어떻게 다른지는 예제로 확인해보자.

```python
@click.command()
@click.option('--count', default=1, help='number of greetings')
@click.argument('name')
def hello(count, name):
    for x in range(count):
        click.echo('Hello %s!' % name)

if __name__ == '__main__':
    hello()        
```

위 코드를 작성한 뒤 —help 를 출력하면 다음과 같다.

```bash
$ python click_sample.py --help
Usage: click_sample.py [OPTIONS] NAME

Options:
  --count INTEGER  number of greetings
  --help           Show this message and exit.
```

option() 으로 표시된 count 는 —와 함께 값을 명시하고, argument 는 옵션 뒤에 인자로 붙는 것을 확인할 수 있다. 실행 결과는 다음과 같다.

```bash
$ python click_sample.py --count 3 nathan
Hello nathan!
Hello nathan!
Hello nathan!
$ python click_sample.py --count=3 nathan
Hello nathan!
Hello nathan!
Hello nathan!
$ python click_sample.py nathan --count 3
Hello nathan!
Hello nathan!
Hello nathan!
```

옵션으로 지정한 count 는 = 가 띄어쓰기로 값을 넣을 수 있고, 옵션과 인자의 순서는 바뀌어도 상관없다.

## 마무리하며

Click 공식 사이트에 Quickstart 를 기반으로 예제들을 살펴보며 간단하게 기능을 살펴보았다. **파이썬**은 쉘 스크립트보다 편리하며 **커맨드라인 도구**를 만들 때 더 쉽게 만들 수 있다. 하지만 옵션이나 인자를 처리하고 여러 커맨드를 중첩시키거나 하는 기능을 구현하기 위해서는 여전히 귀찮은 점들이 많다. **Click** 은 그런 귀찮은 부분을 시원하게 긁어주는 **유용한 라이브러리**이다. 더 자세한 내용은 공식 사이트에 자세하게 문서화 되어 있으니 참고하자.

## 참고

[Click 공식 사이트](https://click.palletsprojects.com/en/7.x/quickstart/)