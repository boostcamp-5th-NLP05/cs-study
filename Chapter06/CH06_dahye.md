# 메모리와 캐시 메모리

# 06-1 RAM의 특징과 종류

## RAM의 특징
  - 주기억장치 중 하나로, '메모리'라는 용어로 지칭됨
  - 실행할 프로그램의 명령어와 데이터가 저장됨
### 휘발성 저장 장치(volatile memory): 전원을 끄면 저장된 내용이 사라지는 저장 장치
  - RAM
  - 실행할 대상 저장
### 비휘발성 저장 장치(non-volatile memory): 전원이 꺼져도 저장된 내용이 유지되는 저장 장치
  - 하드디스크나 SSD 등 보조기억장치가 대표적
  - 보관할 대상 저장
  
## RAM의 용량과 성능
  - RAM 용량이 작으면 보조기억장치에 자주 접근해야 하기 때문에 프로그램 실행시간이 길어지며,
  - RAM 용량이 클 때는 미리 많은 데이터를 RAM에 저장할 수 있어 보조기억장치에 접근하는 횟수가 줄어든다.
  - RAM 용량이 필요 이상으로 커졌을 때 속도가 비례하여 증가하지는 않는다.

## RAM의 종류
### DRAM(Dynamic RAM)
  - 저장된 데이터가 동적으로 변하며, 시간이 지나면 저장된 데이터가 점차 사라진다.
  - 데이터 소멸을 막기 위해 일정 주기로 데이터를 다시 저장해야한다.
  - 일반적으로 사용하는 메모리
  - 장점 : 소비 전력이 비교적 낮다/저렴하다/집적도가 높아 대용량으로 설계하기 용이하다.
### SRAM(Static RAM)
  - 저장된 데이터가 변하지 않으며, 시간이 지나도 저장된 데이터가 사라지지 않는다.
  - 데이터를 재활성화할 필요가 없다.
  - 캐시 메모리(대용량으로 만들어질 필요는 없지만 속도가 빨라야 하는 저장 장치)에 주로 사용된다.
  - 장점 : DRAM보다 일반적으로 속도가 더 빠르다.
  - 단점 : 집적도가 낮다/소비 전력이 크다/가격이 더 비싸다.
### SDRAM(Synchronous Dynamic RAM)
  - 발전된 형태의 DRAM으로 클럭에 맞춰 동작하며, 클럭마다 CPU와 정보를 주고 받는 DRAM
### DDR SDRAM(Double Data Rate SDRAM)
  - 대역폭을 넓혀 속도를 빠르게 만든 SDRAM
  - 대역폭: 데이터를 주고 받는 길의 너비
  - SDRAM의 두배의 대역폭으로 한 클럭당 두 번씩 CPU와 데이터를 주고 받을 수 있음 -> 전송 속도가 SDRAM의 두 배가량 빠름
++ 대역폭 크기에 따라 다음과 같음 (DDRn -> SDR보다 2^n배의 대역폭)
SDR SDRAM(= SDRAM): 한 클럭당 하나씩 데이터를 주고 받는 SDRAM
DDR4 SDRAM: 최근 흔히 사용하는 메모리, SDR SDRAM보다 16배 넓은 대역폭을 가짐



# 06-2 메모리의 주소공간
  - 메모리에 저장된 정보는 시시각각 변함
  - CPU가 프로그램을 이해하는 주소와 메모리 실제 주소에는 차이가 있음
## 물리 주소와 논리 주소
### 물리 주소 : 정보가 실제로 저장된 하드웨어 상의 주소 (메모리 사용)
### 논리 주소 : 실행 중인 프로그램 각각에게 부여된 0번지부터 시작되는 주소 (CPU가 프로그램의 실행 위치를 파악할 때 사용)
### 메모리 관리 장치(MMU): 논리 주소와 물리 주소간 변환을 해주는 장치
  - CPU와 주소 버스 사이에 위치
  - CPU가 발생 시킨 논리 주소 + 베이스 레지스터 값 -> 물리 주소로 변환
### 베이스 레지스터: 프로그램의 가장 작은(첫) 물리 주소를 저장
  - 프로그램 논리 주소 0번지의 물리적 위치를 의미
  - 여기서 논리 주소는 프로그램의 시작점으로부터 떨어진 거리
![물리주소논리주소](https://github.com/boostcamp-5th-NLP05/cs-study/assets/96599427/619f33dd-249a-4201-a711-0c7bab37591f)

## 물리 주소와 논리 주소
CPU는 실행중인 프로그램의 논리 주소 영역을 벗어나는 곳에 명령을 내려서는 안됨 -> 데이터 손상 등 발생 우려
### 한계 레지스터 : 논리 주소의 최대 크기를 저장하는 레지스터
  - CPU 명령이 프로그램 주소 범위를 벗어나는 경우를 방지하는 장치
  - 프로그램 물리 주소의 범위 : 베이스 레지스터 값 ~ (베이스 레지스터 값 + 한계 레지스터 값)
  - CPU가 한계 레지스터보다 높은 논리 주소에 접근하려할 때 인터럽트를 발생시켜 프로그램 실행 중단

 
# 06-3 캐시메모리
## 저장 장치 계층 구조
컴퓨터가 사용하는 저장 장치들이 'CPU에서 얼마나 가까운가'를 기준으로 계층적으로 나타낸 것
### 저장 장치는 일반적으로 아래의 명제를 따름
- CPU와 가까울수록 저장 장치의 속도는 빠르고 멀리있는 저장 장치는 느림
- 속도가 빠른 저장 장치일수록 저장 용량이 작고 가격이 비쌈

![저장장치계층구조](https://github.com/boostcamp-5th-NLP05/cs-study/assets/96599427/26192d1d-8ea7-4a8b-b3d1-a28ffd97d63b)

## 캐시 메모리
  - CPU와 메모리 사이에 위치한 저장 장치로 SRAM 기반
  - 레지스터보다 용량이 크고, 메모리보다 빠르다.
  - CPU의 연산 속도와 메모리 접근 속도의 차이를 줄이려고 사용
### 캐시 메모리의 종류
CPU(코어)와 가까운 순대로 여러 개의 캐시 메모리가 계층을 구성
  - L1 캐시: 코어와 가장 가까운 캐시 메모리
  - L2 캐시
  - L3 캐시
++ 일반적으로 L1과 L2는 코어 내부에, L3는 코어 외부에 존재
### 분리형 캐시 : L1 캐시의 접근 속도를 더 빨리 하기 위해 L1 캐시를 명령어만을 저장하는 L1I 캐시/데이터만 저장하는 L1D 캐시로 분리

## 참조 지역성 원리
캐시 메모리는 CPU가 사용할 법한 대상을 예측해서 저장함
### 캐시 히트 : 자주 사용될 것으로 예측해서 캐시 메모리에 저장한 데이터가 CPU에서 사용된 경우
### 캐시 미스 : 캐시 메모리에 저장했지만 예측이 틀려 CPU가 메모리에서 필요한 데이터를 가져오는 경우
++ 캐시 미스가 자주 발생하면 CPU가 필요한 데이터를 메모리에서 가져오는 경우가 많아져서 성능이 떨어짐
### 캐시 적중률(cache hit ratio): 캐시가 히트되는 비율
  - 캐시 적중률 = 캐시 히트 횟수 / (캐시 히트 횟수 + 캐시 미스 횟수)
  - 캐시 적중률을 높이는 것이 중요!
### 참조 지역성의 원리 : 캐시메모리는 참조 지역성의 원리에 입각해 CPU가 사용할 데이터를 예측
CPU가 주로 메모리에 접근하는 경향을 바탕으로 만들어진 원리
  - CPU는 최근에 접근한 메모리 공간에 다시 접근하는 경향(시간 지역성)이 있다.
  - CPU는 접근한 메모리 공간 근처를 접근하는 경향(공간 지역성)이 있다.
