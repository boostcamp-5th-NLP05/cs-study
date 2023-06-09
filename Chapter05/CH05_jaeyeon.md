# Chapter 05: CPU 성능 향상 기법

## 05-1. 빠른 CPU를 위한 설계 기법

CPU의 사양을 표기할 때 클럭 스피드는 얼마, 스레드는 몇 개 이런 식으로 성능을 표기한 것을 많이 보았을 것이다. 

과연 클럭, 멀티코어, 멀티스레드는 각각 무엇인지 알아보자.

### 클럭

클럭은 CPU에서 사용하는 작업 단위라고 앞서 공부했었다. 

모든 컴퓨터 부품들은 이 클럭 신호에 맞추어 작동하고 CPU는 명령어 사이클에 맞춰 명령어들을 실행한다.

그렇다면 클럭 신호가 빠르게 반복된다면 부품들이 명령어를 처리하는 속도가 빨라지도 명령어 사이클도 더 빠르게 돌 것이다. 

그렇기 때문에 클럭 스피드는 CPU의 성능과 직결된다.

### 코어

코어는 쉽게 말해 일꾼이라고 생각하면 된다. 앞서 배웠던 예시들은 모두 한 명의 일꾼으로 작업을 처리했는데, 

만약 일꾼이 늘어난다면 같은 시간 동안 처리할 수 있는 작업의 양이 늘어나는 것은 자명하다. 

엄밀히 말하자면, 코어는 ‘명령어를 실행하는 부품’이라고 생각하면 되고, 8코어의 경우 이러한 부품이 8개가 들어가 있다고 생각하면 된다. 

이렇게 코어가 여러개 들어가있는 CPU를 멀티코어 CPU 혹은 멀티코어 프로세서라고 부른다.

하지만 코어의 수가 무작정 많다고 작업의 처리량이 무조건 느는건 아니고, 코어마다 작업을 얼마나 효율적으로 분배해주냐도 성능에 있어 중요한 부분이 된다. 

### 스레드

스레드의 사전적 의미는 ‘실행 흐름의 단위’인데, 크게 하드웨어적 스레드와 소프트웨어적 스레드로 나뉜다.

- 하드웨어적 스레드
    
    하드웨어의 관점에서 보면, 스레드는 ‘하나의 코어가 동시에 처리하는 명령어 단위’를 의미한다. 
    
    하드웨어적 스레드가 여러개 있다는 의미는 한 코어 내에서 동시에 여러개의 작업을 처리할 수 있다는 것이다. 
    
    이러한 CPU를 멀티스레드 프로세서 혹은 멀티스레드 CPU라고 부른다. 
    
- 소프트웨어적 스레드
    
    소프트웨어의 관점에서의 스레드는 ‘하나의 프로그램에서 독립적으로 실행되는 단위’를 의미한다. 
    
    하나의 프로그램에서 여러 부분이 한 번에 실행된다면, 이를 소프트웨어적 스레드로 볼 수 있다.
    
- 멀티스레드 프로세서
    
    멀티스레드 프로세서의 핵심은 레지스터이다. 명령어를 실행하기 위해서는 한 세트로 된 여러 레지스터들이 필요한데, 
    
    이러한 세트를 여러개 가지고 있다면 프로세서는 여러개의 명령어를 한 번에 실행할 수 있다.
    

멀티코어 프로세서와 멀티스레드 프로세서를 잘 구분하자!

| CPU | 프로세서 |
| --- | --- |
| 1코어 2스레드 | 멀티스레드 프로세서 |
| 2코어 2스레드 | 멀티코어 프로세서 |
| 2코어 4스레드 | 멀티스레드 & 멀티코어 프로세서 |

## 05-2. 명령어 벙렬 처리 기법

한 번에 여러 명령어들을 처리하기 위해서는 각 스레드들이 병렬적으로 일하게끔 만들어야한다. 이렇게 작동시키는 기법에는 명령어 파이프라이닝, 슈퍼스칼라, 비순차적 명령어처리가 있다.

### 명령어 파이프라인

아래는 세탁기를 예시로 든 파이프라인이다.

![세탁기 파이프라인](https://github.com/boostcamp-5th-NLP05/cs-study/assets/86578246/7acf6b1e-3fc2-4bdd-90d7-99d474f8a926)

출처 : https://jyukki.tistory.com/19

위에 있는 예시처럼 세탁을 하면 4번의 세탁이 매우 오래 걸릴 것이다. 

아래에 있는 예시처럼 진행하면 세탁, 건조, 옷 개기, 옷 정리의 과정이 쉬지 않고 진행돼서 끝나는 시간이 훨씬 빨라진다. 

CPU도 이렇게 일을 시켜야 한다. 명령어 처리 과정을 클럭 단위로 나누어 보면 [명령어 인출 - 명령어 해석 - 명령어 실행 - 결과 저장]의 과정을 따른다. 

CPU는 각 단계가 겹치지만 않는다면, 명령어 1를 해석하면서 명령어 2를 인출해오는 것처럼 동시에 여러 작업이 가능하다. 

이렇게 공장처럼 명령어들을 실행하게끔 만드는 것이 명령어 파이프라이닝이다. 하지만 이런 식으로 작업을 진행할 시 고려해야하는 위험 요소들이 있다.

- 데이터 위험
    
    예를 들어 명령어 1에서 저장되는 데이터를 명령어 2에서 사용한다면, 명령어 2는 명령어 1의 결과 저장 단계가 끝나는 것을 기다려야 올바르게 실행 될 것이다. 
    
    이럴 때 명령어 간의 의존성을 무시하고 실행한다면 원하는 결과가 나오지 않을 것이다.
    
- 제어 위험
    
    제어 위험은 주로 분기와 같은 ‘프로그램 카운터의 갑작스러운 변화’로 인해 일어난다. 
    
    이러한 변화는 앞서 처리하고 있던 명령어들을 쓸모 없게 만들어져 불필요한 처리 과정을 겪게 만든다. 
    
    이러한 과정을 예방하기 위해 분기 예측을 통해 프로그램이 분기할 주소를 예측하여 그 주소를 인출하는 기술이다.
    
- 구조적 위험
    
    구조적 위험은 다른 명령어가 동시에 같은 자원(ALU, 레지스터 등)을 사용하려고 할 때 발생하며, 자원 위험이라고도 한다.
    

### 슈퍼스칼라

슈퍼스칼라는 앞서 배운 파이프라인을 여러개 두는 것을 의미한다. 

슈퍼스칼라 구조로 명령어 처리가 가능한 CPU를 슈퍼스칼라 프로세서 또는 슈퍼스칼라 CPU라고 부른다.

멀티스레드 프로세서는 한 번에 여러 명령어들을 처리할 수 있기 때문에 슈퍼스칼라 구조를 사용할 수 있다.

하지만 파이프라인이 많은 만큼 위험 요소도 따라 증가하기 때문에, 슈퍼스칼라를 설계할 때는 정교한 설계가 필요하다.


### 비순차적 명령어 처리

위 방법들은 모두 명령어를 순서대로 처리하는 방법들인데, 명령어의 처리 순서를 바꿔서 효율적으로 명령어를 처리하는 방법이 비순차적 명령어 처리이다. 

예를 들어 명령어 2는 명령어 1의 결과를 받아서 사용하지만 명령어 3과 4는 독립적이라면, 

1 → 3 → 4 → 2의 순서로 명령어를 처리하면 명령어 2가 명령어 1의 결과 저장 과정을 기다리는 동안 명령어 3과 4의 사이클을 진행할 수 있는 것이다. 

이러한 기법을 적용하기 위해서는 명령어 간의 의존성을 파악할 수 있어야 하고, 순서를 바꿔 실행할 수 있는 명령어에는 어떤 것들이 있는지를 판단할 수 있어야 한다.

## 05-3. CISC와 RISC

### 명령어 집합

CPU의 제조사마다 CPU가 이해하고 실행하는 명령어들이 다르게 생겼다. 

물론 기본적인 구조는 큰 틀을 벗어나지 않지만 명령어의 연산이나 주소 지정 방식은 CPU마다 차이가 있다.

CPU가 이해할 수 있는 명령어들의 모음을 명령어 집합 또는 명령어 집합 구조(ISA)라고 하며, CPU마다 ISA가 다를 수 있다.

이는, 같은 소스 코드로 프로그램을 만들어도 ISA가 다르면 CPU가 이해할 수 있는 명령어와 어셈블리어 또한 달라짐을 의미한다.

ISA는 CPU의 언어이자 하드웨어가 소프트웨어를 어떻게 이해할 지에 대한 약속으로도 볼 수 있다.

가장 널리 쓰이는 CISC와 RISC를 예시로 두 ISA를 비교해보자.

### CISC

> CISC : Complex Instruction Set Computer
> 

여기서 컴퓨터는 CPU라고 봐도 무방하다. CISC를 사용하는 대표적인 운영체제는 x86, x86-64가 있다.

CIST의 가장 큰 특징은 가변 길이 명령어를 사용한다는 것이다. 

명령어들은 모두 다양하고 강력한 기능들의 집합으로 이루어져 있기 때문에, 적은 명령어 개수로도 프로그램을 실행할 수 있다는 특징이 있다.

그렇기 때문에 메모리 공간을 절약하는 측면에서는 효율적인 ISA이다.

하지만 활용하는 명령어가 매우 복잡하고 각 명령어를 실행하는 데에 걸리는 클럭 주기가 일정하지 않기 때문에 파이프라이닝에는 용이하지 않았다는 문제가 있다. 

또한 전체의 20%의 명령어가 전체 명령어 사용량의 80%를 차지하는 등의 문제가 발생하여 대부분의 명령어는 사용 빈도가 낮았다는 문제점이 있었다. 

### RISC

> RISC : Reduced Instruction Set Computer
> 

RISC는 CISC의 단점을 보완하여 등장한 ISA이다. RISC는 명령어의 종류가 작으며 길이가 고정 되어 있다. 

또한 하나의 명령어가 1클럭 내외로 실행되기 때문에 파이프라이닝에 최적화되어 있다고 볼 수 있다.

메모리 접근 명령어를 load, store 두 개로 제한할 정도로 메모리 접근을 단순화하고 최소화하는데, 

그 대신에 레지스터를 적극적으로 활용하기 때문에 레지스터를 이용하는 연산이 많고 범용 레지스터의 개수도 더 많다.

하지만 사용 가능한 명령어의 개수가 CISC보다 적기 때문에 일반적으로 CISC보다는 더 많은 명령어로 프로그램을 작동시킨다.

| CISC(ex. x86) | RISC(ex. MIPS, ARM) |
| --- | --- |
| 복잡하고 다양한 명령어 | 단순하고 적은 명령어 |
| 가변 길이 명령어 | 고정 길이 명령어 |
| 다양한 주소 지정 방식 | 적은 주소 지정 방식 |
| 프로그램을 이루는 명령어의 수가 적음 | 프로그램을 이루는 명령어의 수가 많음 |
| 여러 클럭에 걸쳐 명렁어 수행 | 1클럭 내외로 명령어 수행 |
| 파이프라이닝하기 어려움 | 파이프라이닝하기 쉬움 |
