# Chapter 10 프로세스와 스레드

## 10-1 **프로세스 개요**

- 프로그램 → 프로세스
    - 프로세스 : 실행 중인 프로그램
        - 포그라운드 프로세스
            - 사용자가 보는 앞에서 실행되는 프로세스
        - 백그라운드 프로세스
            - 사용자가 보지 못하는 곳에서 실행되는 프로세스
            
            |  | 유닉스 | 윈도우 |
            | --- | --- | --- |
            | 백그라운드 프로세스 명칭  | 데몬 | 서비스 |
- 프로세스 제어 블록 : PCB [Process Control Block]
    - `커널 영역`에 생성 됨
    - 운영체제는 수많은 프로세스들 사이에서 PCB로 특정 프로세스를 식별하고 해당 프로세스에 필요한 정보를 판단.
    
    | PCB 구성요소 | 프로세스 ID | 특정 프로세스를 식별하기 위해 부여하는 고유한 번호 |
    | --- | --- | --- |
    |  | 레지스터 값 | 자신의 차례가 돌아오면 레지스터의 중간값을 복원하기 위해서 |
    |  | 프로세스 상태 |  |
    |  | CPU 스케쥴링 정보 | 프로세스가 언제, 어떤 순서로 CPU를 할당 받을지에 대한 정보 |
    |  | 메모리 관리 정보 | 베이스 레지스터, 한계 레지스터, 페이지 테이블 정보와 같은 메모리 저장된 위치 정보 |
    |  | 사용한 파일과 입출력장치 목록 |  |
- 문맥 교환
    - 기존 프로세스의 문맥을 `PCB에 백업`하고, 새로운 프로세스를 실행하기 위해 문맥을 PCB 로 부터 복구하여 새로운 프로세스를 실행하는 것

- 프로세스가 생성되면 커널 영역에는 PCB 가 생긴다. 사용자 영역에는 ?
    
    
    | 코드 영역 | 텍스트 영역, 실행할 수 있는 명령어가 저장됨, 쓰기 금지, 읽기 전용 (read-only) | 정적 할당 영역 |
    | --- | --- | --- |
    | 데이터 영역 | - 프로그램이 실행되는 동안 유지할 데이터
    - 전역변수가 저장되는 공간
     | 정적 할당 영역 |
    | 힙 영역 | 프로그래머가 직접 할당할 수 있는 저장 공간
    * 메모리 누수 : 메모리 공간을 반환하지 않고 메모리 내에 계속 남아 메모리 낭비를  초래
    * 낮은 곳에서 위로 | 동적 할당 영역 |
    | 스택 영역 | 데이터를 일시적으로 저장하는 공간
    * 매개 변수, 지역변수
    * 위에서 아래로 | 동적 할당 영역 |

# 10-2 프로세스 상태와 계층 구조

## 프로세스 상태

| 프로세스 상태 | 생성 상태 | 프로세스를 생성 중인 탕태 |
| --- | --- | --- |
|  | 준비 상태 | CPU 를 할당 받지 않았지만 할당 받으면 실행할 수 있는 상태
* 디스패치 : 준비 상태 → 실행 상태 |
|  | 실행 상태 | CPU를 할당받아 실행 중 |
|  | 대기 상태 | 입출력장치의 작업을 기다리는 상태 |
|  | 종료 상태 | 프로세스가 종료된 상태, 운영체제는 PCB와 프로세스를 사용한 메모리를 정리 |

## 프로세스 상태와 계층 구조

- 프로세스는 실행 도중 시스템 호출을 통해 다른 프로세스를 생성 할 수 있다.
- 부모 프로세스
- 자식 프로세스
    - PPID(Parent PID) 가 기록 됨
- 프로세스는 계층 구조로 되어 있음.
- 리눅스의 최초 프로세스 ?
- macOS 최초 프로세스 ?

## 프로세스 생성 기법

- 복제와 옷 갈아입기
- fork : 부모 프로세스가 자식 프로세스를 생성할 때
- exec : 자식 프로세스가 자신의 메모리 공간을 다른 프로그램으로 교체

# 10-3 스레드

- 스레드 : 프로세스를 구성하는 실행의 단위
- 프로세스와 스레드
    - 프로세스끼리는 자원을 공유하지 않지만, 스레드끼리는 같은 프로세스 내의 자원을 공유

| 멀티 프로세스 | 여러 프로세스를 동시에 실행하는 것 |
| --- | --- |
| 멀티 스레드 | 여러 스레드로 프로세스를 동시에 실행하는 것 |

- 프로세스간 통신  = IPC (Inter Process Communication)
    - 프로세스 간의 자원을 공유하고 데이터를 주고 받는 것
    

## [[추가] 파이썬으로 프로세스 다루기](https://github.com/kangtegong/self-learning-cs/blob/main/process/process_python.md#python%EC%9C%BC%EB%A1%9C-%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4-%EB%8B%A4%EB%A3%A8%EA%B8%B0)

## [[추가] 파이썬으로 스레드 다루기](https://github.com/kangtegong/self-learning-cs/blob/main/thread/thread_python.md#python%EC%9C%BC%EB%A1%9C-%EC%8A%A4%EB%A0%88%EB%93%9C-%EB%8B%A4%EB%A3%A8%EA%B8%B0)