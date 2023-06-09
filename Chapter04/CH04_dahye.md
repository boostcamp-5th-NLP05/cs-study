# 04-1 ALU와 제어장치

![컴퓨터구조](./컴퓨터구조.png)

## ALU - CPU 내부에서 계산을 담당
레지스터 - **피연산자**를 받아들이고, 제어장치 - 수행할 연산을 알려주는 **제어신호**를 받아들임
결괏값은 메모리에 저장되지 않고, 일시적으로 레지스터에 저장 (레지스터가 접근속도가 빠르기 때문)
### 플래그
  - ALU가 결괏값 외에 내보내는 것, 추가 정보
  - 연산 결과 > 연산 결과를 담을 레지스터 (오버플로우) -> '결괏값이 너무 크다'라는 추가정보를 내보낸다.
  - 플래그 레지스터에 저장됨
  
++ 이외에도 ALU 내부에는 가산기, 보수기, 시프터, 오버플로우 검출기 등의 여러 계산을 위한 회로들이 있음

## 제어장치 - 제어 신호를 내보내고, 명령어를 해석하는 부품
### 제어 신호
  - 컴퓨터 부품들을 관리하고 작동시키기 위한 일종의 전기 신호
### 제어장치가 받아들이는 정보
  1. 클럭 신호
    - 클럭 : 컴퓨터의 모든 부품을 일사불란하게 움직일 수 있도록 하는 시간 단위
    - 컴퓨터의 모든 부붐은 클럭 신호(박자)에 맞춰서 작동함
  2. 해석해야 할 명령어
    - 명령어 레지스터로부터 해석할 명령어를 받아들이고 해석 -> 제어 신호를 발생 -> 컴퓨터 부품들에 수행해야할 내용을 알려줌
  3. 플래그 레지스터 속 플래그 값
    - 플래그 값을 받아들이고 참고하여 제어 신호를 발생
  4. 시스템 버스 - 제어 버스로 전달된 제어 신호
    - 제어 버스를 통해 외부로부터 전달된 제어 신호를 받아들이기도 함
### 제어장치가 내보내는 정보
  1. CPU 외부에 전달하는 제어 신호
    - 제어 버스로 제어 신호를 내보냄
    - 메모리에 전달하는 제어 신호/ 입출력장치에 전달하는 제어 신호
  2. CPU 내부에 전달하는 제어 신호
    - ALU에 전달하는 제어 신호(수행할 연산 지시)/ 레지스터에 전달하는 제어 신호(레지스터간 데이터를 이동, 레지스터에 저장된 명령어를 해석하기 위함)

# 04-2 레지스터
프로그램 속 명령어와 데이터는 실행 전후로 반드시 레지스터에 저장됨
## 반드시 알아야 할 레지스터
### 프로그램 카운터(명령어 포인터)
  - 메모리에서 가져올 명령어의 주소(메모리에서 읽어 들일 명령어의 주소)를 저장
### 명령어 레지스터
  - 해석할 명령어, 방금 메모리에서 읽어들인 명령어를 저장
### 메모리 주소 레지스터
  - 읽어 들이고자 하는 주소 값을 주소 버스로 보낼 때 메모리의 주소를 저장
### 메모리 버퍼 레지스터(메모리 데이터 레지스터)
  - 데이터 버스로 메모리와 주고받을 값(데이터, 명령어)를 저장
### 범용 레지스터
  - 다양하고 일반적인 상황에서 자유롭게 사용할 수 있는 레지스터
  - 데이터와 주소 모두 저장 가능
### 플래그 레지스터
  - 연산 결과 또는 CPU 상태에 대한 부가적인 정보(플래그) 저장
### 스택 포인터
  - 스택의 꼭대기를 가리키는 레지스터
  - **스택 주소 지정 방식**이라는 주소 지정 방식에 사용
  - 스택 주소 지정 방식 : 가장 최근에 저장한 값부터 꺼내는 스택과 스택 포인터를 이용한 주소 지정 방식
  - 여기서 스택은 메모리 안에 스택 영역에 있음
### 베이스 레지스터
  - 기준 주소의 역할
++ 특정 메모리 주소로 실행 흐름을 이동하는 명령어가 실행되었을 때 순차적인 실행 흐름이 끊기는 경우도 존재

## 특정 레지스터를 이용한 주소 지정 방식 - 스택 주소 지정 방식
위 스택 포인터에 설명
## 특정 레지스터를 이용한 주소 지정 방식 - 변위 주소 지정 방식
(remind..명령어 = 연산코드 + 오퍼랜드)
오퍼랜드 필드의 값(변위)과 특정 레지스터의 값을 더하여 유효 주소를 얻어내는 주소 지정 방식
### 상대 주소 지정 방식
  - 오퍼랜드와 프로그램 카운터의 값을 더하여 유효 주소를 얻는 방식
### 베이스 레지스터 주소 지정 방식
  - 오퍼랜드와 베이스 레지스터의 값을 더하여 유효 주소를 얻는 방식

# 04-3 명령어 사이클과 인터럽트
## 명령어 사이클
하나의 명령어가 처리되는 주기
### 인출 사이클 
  - 메모리에 있는 명령어를 CPU로 가지고 오는 단계
### 실행 사이클
  - CPU로 가져온 명령어를 실행하는 단계
### 간접 사이클
  - 간접 주소 지정 방식으로 메모리 접근이 더 필요한 경우
## 인터럽트
CPU의 작업을 방해하는 신호
### 동기 인터럽트
  - CPU에 의해 발생하는 인터럽트로, 예외라고도 부름
### 비동기 인터럽트(하드웨어 인터럽트)
  - 주로 입출력장치에 의해 발생하는 인터럽트
  - 입출력 작업 중에도 CPU가 효율적으로 명령어를 처리할 수 있게 함
  - 막을 수 있는 인터럽트/없는 인터럽트가 있음
### 하드웨어 인터럽트 처리 순서
  - **인터럽트 요청 신호**(입출력 장치-> CPU) -> 인터럽트 여부 확인 -> **인터럽트 플래그** 확인 -> (가능하다면) 지금까지의 작업 백업 
    -> **인터럽트 벡터**를 참조하여 **인터럽트 서비스 루틴** 실행 -> 끝나면 백업해둔 작업 복구하여 실행 재개
  - 인터럽트 요청 신호 : CPU의 작업을 방해하는 인터럽트에 대한 요청
  - 인터럽트 플래그 : 하드웨어 인터럽트를 받아들일지, 무시할지를 결정하는 플래그
  - 인터럽트 서비스 루틴(인터럽트 핸들러) : 인터럽트를 처리하기 위한 프로그램, 인터럽트가 발생하면 어떻게 행동해야 할지를 알려주는 프로그램
  - 인터럽트 벡터 : 인터럽트 서비스 루틴을 식별하기 위한 정보, 인터럽트 벡터를 통해 인터럽트 서비스 루틴을 처음부터 실행할 수 있음, 인터럽트 요청을 보낸 대상으로부터 데이터 버스를 통해 전달받음
  - 인터럽트가 발생하여 서비스 루틴을 실행해야할 때, 모든 내용을 스택에 백업한다.

++ CPU가 인터럽트를 처리한다 = 인터럽트 서비스 루틴을 실행하고, 본래 수행하던 작업으로 다시 되돌아온다.


  
<좀 더 알아보기>
## 예외의 종류
### 폴트
  - 예외를 처리한 직후 **예외가 발생한 명령어**부터 실행을 재개
### 트랩
  - 예외를 처리한 직후 예외가 발생한 명령어의 **다음 명령어**부터 실행을 재개
### 중단
  - CPU가 실행중인 프로그램을 강제로 중단시킬 수밖에 없는 심각한 오류를 발견했을 때 발생
### 소프트웨어 인터럽트
  - 시스템 호출 발생했을 때 나타남
