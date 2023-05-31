# Chapter 03 명령어

## 03-1 소스 코드와 명령어

개발자가 사용하는 프로그래밍 언어는 사람이 이해하기 쉽게 만들어진 고급 언어이다. 프로그래밍 언어로 작성된 **소스 코드**를 컴퓨터가 이해할 수 있도록 저급 언어(**명령어**)로 변환하는 과정이 필요하다.

### 저급 언어(low level programming language)

컴퓨터가 이해할 수 있는 언어이다.

- **기계어**: 0과 1로 이루어진 언어
- **어셈블리어**: 기계어를 사람이 읽기 편한 형태로 번역한 언어
    > push  rbp\
    > mov   eax, 0

하드웨어와 밀접하게 맞닿아 있는 프로그램을 개발하는 임베디드 개발자, 게임 개발자, 정보 보안 분야 등의 개발자는 어셈블리어를 많이 사용한다.

### 고급 언어(high level programming language

사람이 이해하기 쉽게 만든 언어이다.\
컴퓨터가 이해할 수 있는 저급 언어로 변환하는데 두 가지 방식이 있다.

- **컴파일(compile)** 방식
  - 컴파일러는 소스 코드 전체를 한 번에 저급 언어로 컴파일한다.
  - 소스 코드 내에서 오류가 하나라도 발견되면 컴파일에 실패한다.
  - **목적 코드(object code)**: 컴파일러를 통해 저급 언어로 변환된 코드
  - eg) C
- **인터프리터(interpreter)** 방식
  - 인터프리터가 소스 코드 한 줄씩 차례로 실행한다.
  - 소스 코드 내에서 오류가 발견되어도 그 전 줄까지는 실행된다.
  - eg) Python

<u>인터프리터 방식은 컴파일 방식보다 느리다.</u>\
컴파일 언어는 목적 코드를 한꺼번에 실행하는 반면, 인터프리터는 한 줄 한 줄씩 해석하며 실행해야 하기 때문이다.

> ### 목적 파일 vs 실행 파일
>
> 목적 코드가 실행 파일이 되기 위해 **링킹** 작업이 필요하다.
>
> 예를 들어 `main.c`, `helper.c` 두 소스 코드가 있다고 하자. `main.c`에서 `helper.c`에서 불러온 `help()`를 호출한다.\
> 두 소스 코드를 컴파일 하면 목적 파일 `main.o`, `helper.o`가 생성된다.\
> `main.o`는 파일 내부에 정의되지 않은 기능 `help()`를 사용하기 때문에, `helper.o`와 연결되어야지 실행할 수 있다.\
> 두 파일을 연결하는 과정을 **링킹**이라 한다.

***

## 03-2 명령어의 구조

### 연산 코드와 오퍼랜드

명령어는 **연산 코드**와 **오퍼랜드**로 이루어져 있다.

- **연산 코드(operation code)**, 연산자: 명령어가 수행할 연산
- **오퍼랜드(operand)**, 피연산자: 연산에 사용할 데이터

### 연산 코드

CPU마다 명령어의 종류와 생김새가 다른 것처럼 CPU마다 연산 코드의 종류와 생김새가 다르다.

연산 코드는 크게 네 가지의 유형이 있다.

1. 데이터 전송: move, store, load/fetch, push, pop
2. 산술/논리 연산: add, subtract, multiply, divide, increment, decrement, and, or, not, compare
3. 제어 흐름 변경: jump, conditional jump, halt, call, return
4. 입출력 제어: read, write, start IO, test IO

### 오퍼랜드

오퍼랜드 필드는 데이터 또는 메모리나 레지스터 주소를 담는다. **주소 필드**라고 부르기도 한다.

명령어 안에 있는 오퍼랜드 개수에 따라 `0-주소 명령어`, `1-주소 명령어`, `2-주소 명령어`, `3-주소 명령어`로 부른다.

### 주소 지정 방식(addressing mode)

> **Q. 왜 오퍼랜드 필드에 주소를 담는 걸까?**
>
> 명령어의 길이는 한정되어 있기 때문이다.
>
> 예를 들어 3-주소 명령어는 연산 코드를 뺀 길이를 3으로 나눈 만큼을 오퍼랜드 하나에 할당한다. 이 공간에 데이터를 담는 것보다 메모리 주소에 데이터를 넣고 오퍼랜드 필드에는 주소를 명시하는 것이 더 많은 데이터를 표현할 수 있다.

- **유효 주소(effective address)**: 연산에 사용될 데이터가 저장된 위치. 메모리 주소 또는 레지스터.

주소를 지정하는 대표적인 방식은 다섯 가지가 있다.

|주소 지정 방식|오퍼랜드 필드|장점|단점|
|---|---|---|---|
|**즉시 주소 지정 방식(immediate addressing mode)**|연산에 사용할 데이터|명시할 수 있는 데이터의 크기가 작다|메모리나 레지스터를 접근하지 않아도 돼서 빠르다|
**직접 주소 지정 방식(direct addressing mode)**| 유효 주소(메모리)|즉시 주소 지정 방식보다 명시할 수 있는 데이터가 더 크다|표현할 수 있는 유효 주소에 오퍼랜드 필드 길이만큼 제한이 있다|
**간접 주소 지정 방식(indirect addressing mode)**|유효 주소를 담고 있는 유효 주소(메모리)|표현할 수 있는 유효 주소 범위가 넓다|메모리를 두 번 접근해야 해서 느리다|
**레지스터 주소 지정 방식(register addressing mode)**|레지스터|메모리를 접근하는 방식보다 빠르다|표현할 수 있는 레지스터 크기에 제한이 있다|
**레지스터 간접 주소 지정 방식(register indirect addressing mode)**|유효 주소를 담고 있는 레지스터|간접 주소 지정 방식보다 빠르다|-|

> CPU 외부에 있는 메모리에 접근하는 것보다 CPU 내부에 있는 레지스터에 접근하는 것이 속도가 더 빠르다.