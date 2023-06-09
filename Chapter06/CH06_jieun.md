# Chapter 06 메모리와 캐시 메모리

## 06-1 RAM의 특징과 종류

> - **휘발성 저장 장치(volatile memory)**: 전원을 끄면 저장된 내용이 사라짐
>   - eg) RAM
>   - 실행할 대상 저장
> - **비휘발성 저장 장치(non-volatile memory)**: 전원이 꺼져도 저장된 내용이 유지됨.
>   - eg) 하드 디스크, SSD, CD-ROM, USB 메모리 등 보조기억장치
>   - 보관할 대상 저장

- RAM은 **휘발성 저장 장치**이다.
- CPU가 실행하고 싶은 프로그램이 보조기억장치에 있다면 이를 RAM으로 가져온다.
  - RAM 용량이 크면 많은 프로그램을 동시에 빠르게 실행할 수 있다.
  - 적다면 보조기억장치를 자주 접근해야 해서 실행 시간이 길어진다.
  - RAM 용량이 필요 이상 커졌을 때, 프로그램 실행 속도가 비례해서 커지지는 않는다.

|종류|설명|
|---|---|
|**DRAM(Dynamic RAM)**|- 시간이 지나면 저장된 데이터가 점차 사라져서 일정 주기로 데이터 재활성화 필요<br>- 소비 전력이 비교적 낮고, 저렴하고, 집적도가 높기 때문에 대용량으로 설계하기 용이|
|**SRAM(Static RAM)**|- 저장된 데이터가 사라지지 않아서 주기적 재활성화 필요 없음<br>- DRAM보다 속도 빠름<br>- DRAM보다 집적도가 낮고, 소비 전력도 크며, 가격도 더 비쌈<br>- 캐시 메모리에서 사용|
|**SDRAM(Synchronous Dynamic RAM)**|- 클럭 신호와 동기화: 클럭마다 CPU와 정보를 주고 받는 DRAM<br>- **SDR SDRAM(Single Data Rate SDRAM)**|
|**DDR SDRAM(Double Data Rate SDRAM)**|- 대역폭(data rate): 데이터를 주고받는 길의 너비<br>- 한 클럭당 두 번씩 CPU와 데이터를 주고받음<br>- DDR2 SDRAM: DDR SDRAM의 대역폭 2 배, SDRAM 4배<br>- DDR3 SDRAM: DDR2 SDRAM 대역폭 2배, SDRAM 8배<br>- 최근에는 DDR4 SDRAM:  SDRAM의 16배

## 06-2 메모리의 주소 공간

- 주소의 두 종류
  - **물리 주소**: 정보가 실제로 저장된 하드웨어상의 주소
    - 메모리가 사용
  - **논리 주소**: 실행 중인 프로그램 각각에게 부여된 0번지부터 시작되는 주소
    - CPU, 프로그램 사용
- 변환 방법
  - **메모리 관리 장치(MMU: Memory Management Unit)**: CPU와 주소 버스 사이에 위치
  - 논리 주소에 *베이스 레지스터* 값을 더해서 물리 주소로 변환
  - *베이스 레지스터*에는 프로그램의 첫 물리 주소 저장
- 메모리 보호 기법
  - **한계 레지스터**: 논리 주소의 최대 크기 저장
  - 프로그램의 범위에 벗어난 메모리 공간 접근 방지
  - CPU가 한계 레지스터보다 높은 논리 주소에 접근하려고 하면 인터럽트(트랩) 발생

## 06-3 캐시 메모리

### 저장 장치 계층 구조(memory hierarchy)

- 기준: CPU에 얼마나 가까운가
- 명제
  1. CPU와 가까운 저장 장치는 빠르고, 멀리 있는 저장 장치는 느리다.
  2. 속도가 빠른 저장 장치는 저장 용량이 작고, 가격이 비싸다

<img alt="memory hierarchy" width=600 src="https://github.com/boostcamp-5th-NLP05/cs-study/assets/81620001/f7cf694a-ef16-4ddd-8d4a-7e42fecd5ba2"> \
사진 출처: 강민철, <혼자 공부하는 컴퓨터 구조 + 운영체제>, 한빛미디어(2022)

### 캐시 메모리

<img alt="cache memory" width=600 src="https://github.com/boostcamp-5th-NLP05/cs-study/assets/81620001/c8a732f2-5bcc-48a6-82cb-59ba248a06b0"> \
사진 출처: 강민철, <혼자 공부하는 컴퓨터 구조 + 운영체제>, 한빛미디어(2022)

- 레지스터보다 용량이 크고 메모리보다 빠른 SRAM 기반 저장 장치
- CPU의 연산 속도와 메모리 접근 속도의 차이를 줄이기 위해 탄생
- L1 캐시, L2 캐시, L3 캐시
  - 분리형 캐시: L1 -> L1I(명령어만 저장), L1D(데이터만 저장)
- CPU가 사용할 법한 대상을 예측하여 저장
  - **캐시 히트**: 캐시 메모리 내 데이터가 CPU에서 활용될 경우
  - **캐시 미스**: 메모리에서 필요한 데이터를 직접 가져와야 하는 경우
  - **캐시 적중률** = 캐시 히트 횟수 / (캐시 히트 횟수 + 캐시 미스 횟수)
- 메모리로부터 가져올 데이터를 결정하는 원칙: **참조 지역성 원리(locality of reference, principle of locality)**

|참조 지역성 원리|설명|
|---|---|
|**시간 지역성(temporal locality)**|- 최근에 접근했던 메모리 공간에 다시 접근하려는 경향<br>- 변수에 값을 저장하고 나중에 다시 참조|
|**공간 지역성(spatial locality)**|- 접근한 메모리 공간 근처를 접근하려는 경향<br>- 프로그램은 보통 관련 데이터들끼리 한데 모여 있음. 예를 들어 사용자가 입력을 할 적에는 입력 기능이 모여 있는 공간 근처를 집중적으로 접근|
