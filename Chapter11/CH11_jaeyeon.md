# Chapter 11. CPU 스케줄링

## 11-1. CPU 스케줄링 개요

### 프로세스 우선순위

우선순위가 높은 프로세스를 빨리 처리하는 것이 왜 효율적일까?

프로세스는 크게 아래와 같이 나눌 수 있다.

| 입출력 집중 프로세스 | CPU 집중 프로세스 |
| --- | --- |
| 비디오 재생, 디스크 백업 | 수학 연산, 컴파일, 그래픽 처리 작업 |
| 입출력을 위한 대기 상태에 많이 머무름 | 실행 상태에 더 많이 머무름 |

입출력 집중 프로세스를 먼저 진행 시켜 대기 상태로 만든 후, 프로세스가 대기 상태인 동안

CPU 집중 프로세스를 처리 하는 것이 효율적인 처리 방법이라고 볼 수 있다.

그렇기 때문에 프로세스마다 우선순위를 부여하고 우선순위에 따라 프로세스를 실행 시키는 것이 효율적이다.

## 스케줄링 큐

CPU가 일일이 PCB를 돌아보며 실행할 프로세스를 찾는 것은 비효율적이기 때문에,

스케줄링 큐를 두어 다음에 실행시킬 프로세스를 빠르게 찾을 수 있도록 한다.

크게 **준비 큐**는 CPU를 이용하고 싶은 프로세스들이 서는 줄,

**대기 큐**는 입출력장치를 이용하기 위해 대기 상태에 접어든 프로세스들이 서는 줄을 의미한다.

준비 큐에 있는 프로세스 중 우선순위가 높은 프로세스를 먼저 실행하고,

입출력 요청이 발생하면 대기 큐로 이동하며, 입출력이 완료되면 운영체제는

대기 큐에서 작업이 완료된 PCB를 찾고 이 PCB를 준비 상태로 변경한 뒤 대기 큐에서 제거하고

PCB는 다시 준비 큐로 이동한다.

### 선점형과 비선점형 스케줄링

- 선점형 스케줄링(preemptive scheduling)
    
    프로세스가 CPU를 비롯한 자원을 사용하고 있더라고 운영체제가 프로세스로부터 
    
    자원을 강제로 빼앗아 다른 프로세스에 할당할 수 있는 스케줄링을 의미한다.
    
    한 프로세스의 자원 독점을 막을 수 있지만, 문맥 교환 과정에서 오버헤드가 발생할 수 있다.
    
- 비선점형 스케줄링(non-preemptive scheduling)
    
    하나의 프로세스가 자원을 사용하고 있다면 그 프로세스가 종료되거나
    
    스스로 대기 상태에 접어들기 전까진 다른 프로세스가 끼어들 수 없는 스케줄링을 의미한다.
    
    문맥 교환의 오버헤드는 적지만 모든 프로세스가 골고루 자원을 사용할 수 없다는 단점이 있다.
    

대부분의 운영체제는 선점형 스케줄링 방식을 차용하고 있다.

## 11-2. CPU 스케줄링 알고리즘

### 스케줄링 알고리즘의 종류

- 선입 선처리 스케줄링(First-Come-First-Serve Scheduling)
    
    준비 큐에 삽입된 순서대로 프로세스들을 처리하는 비선점형 스케줄링이다.
    
    프로세스들이 기다리는 시간이 매우 길어질 수 있다는 점에서 부작용이 있다.
    
    앞선 프로세스들을 모두 기다려야하기 때문에 호위 효과(convoy effect)가 발생할 수 있다.
    
- 최단 작업 우선 스케줄링(Shortest-Job-First Scheduling)
    
    CPU 사용 시간이 짧은 프로세스부터 처리하는 비선점형 스케줄링이다.
    
- 라운드 로빈(Round-Robin Scheduling)
    
    선입 선처리 스케줄링에 타임 슬라이스라는 개념이 더해진 스케줄링 방식이다.
    
    타임 슬라이스란 각 프로세스가 CPU를 사용할 수 있는 정해진 시간을 의미한다.
    
    타임 슬라이스의 크기를 잘 정해야하는데, 타임 슬라이스가 너무 작으면
    
    문맥 교환으로부터의 오버헤드가 커지게 된다.
    
- 최소 잔여 시간 스케줄링(Shortest Remaining Scheduling)
    
    최단 작업 우선 스케줄링 알고리즘과 라운드 로빈 알고리즘을 합친 스케줄링이다.
    
    프로세스들은 정해진 타임 슬라이스만큼 CPU를 실행하되, CPU를 사용할 다음 프로세스는
    
    남아있는 작업 시간이 가장 적은 프로세스가 선택된다.
    
- 우선순위 스케줄링(Priority Scheduling)
    
    프로세스들에 우선순위를 부여하고, 가장 높은 우선순위를 가진 프로세스부터 실행하는 스케줄링 알고리즘이다.
    
    우선순위가 같으면 선입 선처리로 스케줄링 된다.
    
    하지만 치명적인 단점은, 우선순위가 낮은 프로세스는 계속 CPU를 할당 받지 못해 기아(starvation) 현상이 일어날 수 있다.
    
    그렇기 때문에 오래동안 대기한 프로세스의 우선 순위를 점차 높이는 에이징(aging) 방식을 사용할 수 있다.
    
- 다단계 큐 스케줄링
    
    우선순위별로 준비 큐를 여러 개 사용하는 방식이다.
    
    다단계 큐 스케줄링 하에서는 우선순위가 가장 높은 큐에 있는 프로세스들을 먼저 처리하고,
    
    우선순위가 가장 높은 큐가 비어 있으면 그다음 우선순위 큐에 있는 프로세스들을 처리한다.
    
    이런 식으로 큐를 여러 개 두면 프로세스 유형별로 우선순위를 구분하여 실행하는 것이 편리해진다.
    
- 다단계 피드백 큐 스케줄링
    
    다단계 큐 스케줄링의 발전된 형태이다.
    
    프로세스들이 큐 사이를 이동할 수 있기 때문에 대기 시간이 길어지면 우선 순위가 높은 큐로 이동하며
    
    에이징 기법을 적용하여 기아 현상을 방지한다.
    
    구현은 복잡하지만 가장 일반적인 CPU 스케줄링 알고리즘이다.
