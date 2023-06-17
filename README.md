# Smart-Farm-Project
Arduino Project - Farm Smart Monitoring System

## 프로젝트 기간
2022.12 ~ 2023.01

## 프로젝트 요약
- Arduino를 활용해서 스마트팜 기반의 농작물 모니터링 시스템을 만든다.
- 농촌 인구 감소 및 노령화로 인한 농업 인구 부족 → 농업 노동력을 자동화로 대체 시스템 구현

## 프로젝트 개요
1. 야생동물로부터 보호를 위한 농장 보안 시스템
2. 재배 환경의 데이터 수집을 통한 데이터 구축
3. 타이머 기능을 이용한 자동화 시스템 구축으로 노동력 부족현상 완화
4. 농업인구의 고령화로 자동화 시스템에 대한 쉬운 작동 서비스

## 프로젝트 사용 기술
- C++
- Arduino Sensor
- Serial communication

## 시스템 구성
<p align="center"><img src="https://github.com/kmj0505/smart-farm-project/assets/123744547/650b59e0-28e3-478b-9f5b-5ed9952c58bb;" style="width: 100vw; min-width: 300px;" /></p>

### 1) 주 시스템
<p align="center"><img src="https://github.com/kmj0505/smart-farm-project/assets/123744547/3817487e-6fa6-4a51-ace3-ad6e6fb31ef4" style="width: 30vw; min-width: 200px;"/></p>
- 주 시스템에서는 사용자가 사용하는 시스템으로 구성하였다.
- 사용자 인터페이스를 나타내는 LCD와 보안을 위한 비밀번호 입력, 보조 시스템에 데이터와 제어를 요청하는 4x4 키패드로 구성하였다.


### 1-1) 사용자 인터페이스 & 버튼 제어
<p align="center"><img src="https://github.com/kmj0505/smart-farm-project/assets/123744547/ec2f69c7-60cf-4903-bf7e-eec4c62e7a2e" /></p>
<p align="center"><img src="https://github.com/kmj0505/smart-farm-project/assets/123744547/d3cdef1c-e507-44b8-85fb-6cd6111be4b7" /></p>
- 사용자 인터페이스는 버튼 구성도에 맞게 LCD 화면에서 목록 화하였고, 각 목록의 버튼을 누르면 주 시스템에서 보조 시스템으로 명령어를 보낸다. 이후 다시 보조 시스템에서 주 시스템으로 보낸 데이터를 화면에 띄우게 된다.


### 2) 보조 시스템
<p align="center"><img src="https://github.com/kmj0505/smart-farm-project/assets/123744547/1dda1591-da6f-4557-b0ca-f838be7a97c3"  style="width: 30vw; min-width: 200px;" /></p>
<p align="center"><img src="https://github.com/kmj0505/smart-farm-project/assets/123744547/d5e359f7-f8ed-4872-9349-bdbc936a7003"  style="width: 30vw; min-width: 200px;" /></p>
- 보조 시스템은 주 시스템에서 요청하는 데이터와 제어 요청을 수행하는 시스템으로 구성하였다. 아두이노 간 통신은 RX, TX 핀을 교차하여 시리얼 통신인 I2C을 이용하였다.
- 온도, 조도, 습도 센서를 사용해 실시간 환경 데이터를 수집할 수 있고, LCD에는 실시간으로 날짜 및 시간 출력을 나타내었다.
- 릴레이 모듈은 온풍기와 연결되어 있다는 가정을 하여 버튼과 연결하여 누르게 되면 외부 인터럽트가 발생되어 비상 상황을 알리고 전원이 차단되며 부저가 울릴 수 있게 구현하였다.

### 2-1) 주 시스템과의 통신
<p align="center"><img src="https://github.com/kmj0505/smart-farm-project/assets/123744547/76d92181-0039-4bd4-b74d-010c44f3dba8" /></p>
- 주 시스템에서 버튼을 누르면 보조 시스템에 각 버튼에 저장된 문자 명령어를 보내게 되는데, 이때 보조 시스템은 주 시스템이 보낸 문자를 str 변수에 저장하여 어떤 명령어인지 비교해서 주 시스템이 요청한 데이터를 보내게 된다.
- 센서에서 얻는 데이터일 경우, 주 시스템으로부터 센서 데이터를 요청하는 명령어를 받았을 때 데이터를 수집해서 보낼 수 있게 설정하였다.
- relay_state 변수는 0일 경우 off, 1일 경우 on으로 설정하였고, 주 시스템으로부터 릴레이 관련 명령어를 받게 되면 릴레이의 상태를 확인해 digitalWrite() 함수로 릴레이를 제어한다.

### 2-2) 외부 인터럽트
<p align="center"><img src="https://github.com/kmj0505/smart-farm-project/assets/123744547/15e5c21b-4cf5-4a86-ac3c-ef659018968e" /></p>
- 보조 시스템의 외부 인터럽트는 비상 버튼을 누르면 발생하는 Falling 방식을 사용해 구현하였다. 아두이노 자체 풀업 방식을 사용하였고, 인터럽트가 발생하면 위 그림의 abcd() 함수가 실행 되어 전원과 연결된 릴레이를 끄고 비상을 알리는 부저를 켜도록 설정하였다.

### 2-3) 타이머 인터럽트
- 보조 시스템의 타이머 인터럽트는 loop() 함수가 동작하는 약 1초의 시간 동안, 0.5초 간격으로 인터럽트가 발생되어 현재 날짜와 시간 데이터를 RTC 모듈에서 받아서 LCD 화면에 띄우는 설정을 통해 시계처럼 보일 수 있도록 구현하였다.

## 기대효과

- 많은 고령화 농촌 인구의 노동력 절감으로 효율성 상승
- 첨단 기술과 농업의 융합으로 청년 일자리 창출
- 모니터링 시스템을 활용해 최적의 재배 환경 유지로 계절에 상관없이 좋은 품질의 작물 재배 가능
- 재배 환경 유지로 지구온난화 등 외부 환경 요인에 상관없이 식량 위기 완화
