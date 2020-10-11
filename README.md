# 2051_영차영차

### 제 18회 임베디드 SW경진대회 [자율주행 모형자동차]

## 개발 목적 및 목표

- 자율주행 모형자동차의 하드웨어를 제어하기 위한 소프트웨어를 임베디드 환경에서 제작함으로써, 임베디드 소프트웨어 제작 기술을 습득할 수 있고, 이를 통해서 자율주행의 개념을 이해할 수 있다.

- 라인인식에 대한 기능을 구현한 뒤 자율주행 상황에서 발생할 수 있는 위험요소나 그 외 사용자를 편리하게 할 수 있는 여러 기능들을 논의하면서 대회에서 수행해야하는 미션들과 비교하여 실제 자동차에 적용할 수 있는 알고리즘 구현을 목표로 한다.

## 개발 방법

### 1. 개발 환경 구축

- 노트북에 ubuntu 16.04를 설치하여 Linux 환경을 제공하는 host pc로 활용하였고, 터미널 편집기를 통하여 소스코드를 작성하였다. 모형자동차(target board)는 Embedded Linux 기반의 os 를 사용하며, serial 통신 및 ethernet 을 통해 host pc와 상호작용한다.

<p ![Architecture](./image/Architecture.png)></p>

개발 환경 구조도

- serial 통신을 통해 target board의 터미널을 확인함으로써 센서값, 영상 픽셀값 등을 출력하여 디버깅에 활용하였으며 ethernet을 통해 host pc에서 작성한 실행파일의 데이터를 target board에 전송하였다.

- c언어를 기반으로 소스코드를 작성하였으며, 차선인식 및 신호등과 같이 영상처리가 필요한 부분은 c++언어를 활용하였다. 이때 예제에서 주어진 OpenCV라이브러리의 함수를 적극적으로 활용하여 작성하였다.

### 2.Thread 구조를 통한 스케쥴링

- 스레드간 메모리충돌을 막기 위해 mutex 잠금을 통해 동기화하였으며, 조건변수를 활용하여 스레드 간의 호출에 이용하였다. 이때 각 스레드는 블락되었다가 호출에따라 활성화를 반복한다 . 스레드는 capture_thread , sensor_thread 그리고 각 미션 스레드들로 구성된다.

<p ![Thread](./image/Thread.png)></p>

**스레드 구조도**

- capture_thread는 영상처리 스레드 및 주 스레드로써, 일정 주기마다 sensor_thread를 호출한다. 호출에 따라 sensor_thread가 라인 감지용 IR 센서, 벽을 감지하는 적외선 PSD 센서를 통해 미션을 검출한다. 이때 mode_count 에 따라 해당하는 mission에 대한 센서값만을 검출하도록 한다.

- 조건을 만족하면 각 미션에 해당하는 mission thread를 호출한다. 각 mission thread는 미션을 수행하고, 스스로 종료한다.

### 3. 소프트웨어 구성

<p ![software](./image/software.png)></p>

소프트웨어 구조도

- 소프트웨어는 크게 스레드의 호출과 주행함수를 포함하는 main.c 파일, 영상처리 관련 함수들을 포함하는 exam.cpp 파일, 미션함수를 포함하는 mission.c 파일로 구성되어 있다.

## 개발의 차별성

### **효율적인 소스파일 구성**

- 소스코드를 작성하는데 있어서 효율적이고, 시간을 단축하기 위해서는 가독성이 좋고, 값을 수정하는데 있어서도 편리해야 한다. 따라서 소스파일을 구성할 때 variable.h 파일, struct.h 파일과 같이 변수 및 구조체 선언파일을 따로 분리해 둠으로써 변수들을 한꺼번에 관리하도록 하였다.

### **가변 ROI 설정**

- 차선을 검출하는데 있어서 각 차선 구간에 대한 ROI를 달리 설정하여 차선이 교차되어있는 곳에서도 별다른 조치나 변동사항 없이 정상적으로 주행하게 하였다. 또한 정지선 인식을 위한ROI를 넓게 설정하여 정지선을 밟기 전에 안정적으로 감속하도록 하였다.

## 개발 일정

<p ![calendar](./image/calendar.png)></p>


## 팀 업무 분장

<p ![part](./image/part.png)></p>
