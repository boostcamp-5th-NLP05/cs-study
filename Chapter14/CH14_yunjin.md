# Chapter 14 가상 메모리

# 14-1 연속 메모리 할당

- 연속 메모리 할당 : 프로세스에 연속적인 메모리 공간을 할당하는 방식
- 스와핑
    - 현재 사용되지 않는 프로세스들을 보조기억장치의 일부 영역으로 쫓아 내고 그렇게 생긴 빈 공간에 새 프로세스 적재하여 실행하는 방식
    - `스왑 아웃`
        - 현재 실행되지 않는 프로세스가 `메모리에서 스왑 영역으로 옮겨지는 것`을 스왑 아웃이라 한다.
    - `스왑 인`
        - `스왑 영역에 있던 프로세스가 다시 메모리로` 옮겨지는 것 (물리 주소는 달라질 수 있다.)
    

    - 장점 : 프로세스들이 요구하는 메모리 주소 공간의 크기가 실제 메모리 크기보다 큰 경우에도 프로세스들을 동시에 실행할 수 있음.
        - 프로세스들이 요구하는 메모리 공간 크기 실행 가능 > 실제 메모리 크기
    - 스왑 영역 크기 확인 명령어
        - free, top 명령어
- 메모리 할당
    - 메모리의 빈 공간에 프로세스를 할당하는 방식
        - `최초 적합`(First fit)
            - 운영체제가 메모리 내의 빈 공간을 순서대로 검색하다 적재할 수 있는 공간을 발견하면 그 공간에 프로세스를 배치하는 방식
        - `최적 적합`(Best fit)
            - 운영체제가 빈 공간을 모두 검색해본 뒤, 적재 가능한 가장 작은 공간에 할당
        - `최악 적합` (Worst fit)
            - 운영체제가 빈 공간을 모두 검색해본 뒤, 적재 가능한 가장 큰 공간에 할당

- `외부 단편화` (External Fragmentation)
    - 프로세스를 할당하기 어려울 만큼 작은 메모리 공간들로 인해 메모리가 낭비되는 현상을 의미

- 외부 단편화 해결 방법
    - 메모리 압축
        - 여기저기 흩어져 있는 빈 공간들을 하나로 모으는 방식
        - 수 많은 오버 헤드를 야기할 수 있음.
    - Next, 페이징 기법

# 14-2 페이징을 통한 가상 메모리 관리

- 연속 메모리 할당의 두 가지 문제점
    - 외부 단편화
    - 물리 메모리보다 큰 프로세스 실행 불가
- 가상 메모리
    - **실행하고자 하는 프로그램을 일부만 메모리에 적재하여 실제 물리 메모리 크기보다 더 큰 프로세스를 실행할 수 있게 하는 기술**
    - 종류 : 페이징, 세그멘테이션
        - **페이징(Paging)**
            - 메모리의 물리 주소 공간을 프레임 단위로 자르고, 프로세스의 논리 주소 공간을 페이지 단위로 자른 뒤 각 페이지를 프레임에 할당하는 가상 메모리 관리 기법
            - 페이지 아웃(page out) = 스왑 아웃
            - 페이지 인 (page in) = 스왑 인
        - **~~세그멘테이션(Segmentation)~~**
    
- `페이지 테이블`
    - 페이징 시스템은 프로세스가 비록 물리 주소에 불연속적으로 배치되더라도 (CPU가 바라보는 주소인) 논리 주소에는 연속적으로 배치되도록 페이지 테이블을 이용
    - 페이지 테이블은 페이지 번호와 프레임 번호를 짝지어 주는 일종의 이정표
    - 프로세스마다 페이지 테이블 있다.
    - CPU는 그저 논리 주소를 실행하면 될 뿐임.
    - 내부 단편화 문제
        - 하나의 페이지 크기보다 작은 크기로 발생 → 페이지 크기가 작다면 내부 단편화의 크기가 작아짐, 대신 페이지 테이블이 차지하는 공간이 낭비 → 페이지 크기 조정 중요
    - PTBR = 페이지 테이블 베이스 레지스터
        - 프로세스의 페이지 테이블이 적재된 주소를 가리킴
    - TLB (Translation Lookaside Butter)
        - `CPU 곁에  페이지 테이블의 캐시 메모리`
        - 페이지 테이블의 일부를 가져와 저장.
        - TLB 히트
        - TLB 미스
        - 페이지 테이블의 캐시 메모리 역할을 수행하기 위해 페이지 테이블의 일부를 저장
- **페이징에서의 *주소* 변환**
    - 변환을 위해서 두 가지 정보 필요
        - 어떤 페이지/프레임에 접근하고 싶은지
        - 접근하려는 주소가 그 페이지 혹은 프레임으로부터 얼마나 떨어져 있는지 ?
    - 페이징 시스템의 논리 주소
        - 페이지 번호(page number)
        - 변위(offset)
- PTE (페이지 테이블 엔트리)(Page Table Entry)
    - 유효 비트(Valid bit)
        - 유효 비트는 현재 해당 페이지에 접근 가능한지 여부를 알려줌
        - 페이지가 메모리에 적재되어 있다면 유효 비트 1, 아니면 0
        - 0일 때 접근 → 페이지 폴트 예외 발생
        - 페이지 폴트 처리
            - CPU 는 기존의 작업 내역을 백업
            - 페이지 폴트 처리 루틴 실행
            - 페이지 처리 루틴은 원하느 페이지를 메모리로 가져온 뒤 유효 비트를 1로 변경
            - 페이지 폴트를 처리했다면 이제 CPU 는 해당 메모리에 접근
    - 보호 비트(protection bit)
        - 페이지 보호 기능을 위해 존재하는 비트
        
        | r(Read) | w(Write) | x(eXecute) |
        | --- | --- | --- |
        | 1 | 0 | 0 |
        | 0 | 1 | 0 |
        | 1 | 0 | 1 |
        | 1 | 1 | 1 |
    - 참조 비트(reference bit)
        - CPU가 이 페이지에 접근한 적이 있는지 여부를 나타냄
        - 적재 이후 CPU 가 읽거나 쓴 페이지는 참조 비트가 1로 세팅, 아니면 0
    - 수정 비트(prodified bit)
        - 해당 페이지에 데이터를 쓴 적이 있는지 없는지 수정 여부를 알려 줌
        - 더티 비트 (dirty bit)
            - 수정된 적이 있는 페이지가 스왑 아웃 될 경우 변견된 값을 보조기억 장치에 기록하는 작업이 추가되어야 한다. 이것을 표시하기 위해 수정 비트를 둠.

- 페이징의 이점
    - 쓰기 시 복사
    - fork
    - 같은 메모리 공간을 가리키다, 쓰기시에 별도의 공간으로 페이지가 복제 됨
- 계층적 페이징
    - 프로세스 테이블의 크기가 크다는 부담 → 프로세스를 이루는 모든 페이지 테이블 엔트리를 항상 메모리에 유지하지 않을 수 있는 방법이 등장
    - 다단계 페이지 테이블
    - 바깥 페이지 테이블
    - 안쪽 페이지 테이블
    - 방식
        - 바깥 페이지 번호를 통해 페이지 테이블의 페이지를 찾기
        - 페이지 테이블의 페이지를 통해 프레임 번호를 찾고 변위를 더함으로서 물리 주소 얻기

# 14-3 페이지 교체와 프레임 할당

- 요구 페이징
    - 처음부터 모든 페이지를 적재하지 않고 필요한 페이지만을 메모리에 적재하는 기법
    - 요구되는 페이지만 적재하는 기법
    - 방식
        - CPU 가 특정 페이지에 접근하는 명령어 실행
        - 해당 페이지가 현재 메모리에 있을 경우 (유효 비트가 1 일 경우 ) CPU 는 페이지가 적재된 프레임에 접근
        - 해당 페이지가 현재 메모리에 없을 경우 (유효 비트가 0일 경우) 페이지 폴트가 발생
        - 페이지 폴트 처리 루틴은 해당 페이지를 메모리로 적재하고 유효 비트를 1로 설정
- `안정적으로 작동하기 위해선?`
    - 페이지 교체
    - 프레임 할당
- 페이지 교체 알고리즘
    - 페이지 폴트가 적은 알고리즘 → 페이지 폴트 횟수 → 페이지 참조열
    - 알고리즘 종류
        - FIFO 페이지 교체 알고리즘 (First-In First-Out Page Replacement Algorithm)
            - 적재된 페이지 순서대로 교체하는 알고리즘
        - 최적 페이지 알고리즘
            - 앞으로의 사용 빈도가 가장 낮은 페이지를 교체하는 알고리즘
            - 구현이 어려움
        - LRU 페이지 교체 알고리즘
            - 가장 오랫동안 사용되지 않은 페이지를 교체하는 알고리즘

- 스래싱과 프레임 할당
    - 스레싱(Thrasing)
        - 프로세스가 실제 실행되는 시간보다 페이징에 더 많은 시간을 소요하여 성능이 저해되는 문제
    - CPU 이용률, 멀티프로그래밍 정도 그래프
    - 각 프로세스가 필요로 하는 최소한의 프레임 수가 보장되지 않았기 때문이다.
    - 프레임 할당 방식
        - 균등 할당
            - 모든 프로세스에 균등하게  프레임을 할당
        - 비례 할당
            - 프로세스 크기에 따라 프레임을 배분하는 방식
        - 작업 집합 모델 (working set model)
            - 작업 집합 모델 기반 프레임 할당은 작업 집합의 크기만큼만  프레임을 할당하는 방식이다.
            - 작업 집합 : 실행 중인 프로세스가 일정 시간 동안 참조한 페이지의 집합
                - 필요한 두 가지: 프로세스가 참조한 페이지, 일정 시간 간격
        - 페이지 폴트 빈도(PFF; Page-Fault Frequency)
            - 페이지 폴트율에 상한선과 하한선을 정하고, 그 내부 범위에서만 프레임을 할당하는 방식
            - 페이지 폴트율이 상한선보다 더 높아지면 프레임 더 할당
            - 페이지 폴트율이 하한선보다 더 낮아지면 프레임 회수