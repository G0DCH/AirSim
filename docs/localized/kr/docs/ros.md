# 로봇 운영 체제(ROS)에서 AirSim을 사용하는 방법

AirSim과 ROS는 C++ 또는 Python을 사용하여 통합 할 수 있습니다. AirSim에서 ROS 주제로 데이터를 게시하는 방법을 보여주는 일부 예제 ROS 노드가 제공됩니다.

# Python

## 전제 조건

이 지침은 Ubuntu 16.04, ROS Kinetic, UE4 4.18 이상 및 최신 AirSim 릴리스 용입니다.
계속 진행하기 전에 이러한 구성 요소를 설치하고 작동해야합니다

## Setup


### 다음 지시 사항에 따라 catkin 작업 공간에 새 ROS 패키지를 작성하십시오.

airsim 또는 원하는대로 ROS 패키지를 새로 만듭니다.

[ROS 패키지 생성](http://wiki.ros.org/ROS/Tutorials/CreatingPackage)

catkin 작업 공간이 없는 경우 먼저 ROS 초보자 자습서를 통해 작업해야 합니다.

### ROS 패키지에 AirSim ROS 노드 예제 추가

ROS 패키지 디렉토리에서 ros 예제를 AirSim/PythonClient 디렉토리에서 ROS 패키지로 복사하십시오. AirSim 및 catkin 작업 공간 경로와 일치하도록 아래 코드를 변경하십시오.

```
# copy package
mkdir -p ../catkin_ws/src/airsim/scripts/airsim
cp AirSim/PythonClient/airsim/*.py ../catkin_ws/src/airsim/scripts/airsim

# copy ROS examples
cp AirSim/PythonClient/ros/*.py ../catkin_ws/src/airsim/scripts
```


### ROS AirSim 패키지 구축

디렉토리를 최상위 catkin 작업 공간 폴더 (예: ```cd ~/catkin_ws```)로 변경하고 ```catkin_make```를 실행하십시오.
이렇게하면 airsim 패키지가 만들어집니다. 다음으로 ```source devel/setup.bash```를 실행하면 ROS가 새 패키지를 찾을 수 있습니다.
이 명령을 ~/.bashrc에 추가하여 catkin 작업 공간을 자동으로 로드 할 수 있습니다.

## ROS AirSim 노드를 실행하는 방법

먼저 UE4가 airsim 프로젝트를 실행하고 자동차 또는 드론을 선택하고 시뮬레이션이 실행 중인지 확인하십시오.
예는 자동차 또는 드론을 지원합니다. ros 예제를 실행하기에 올바른 차량이 있는지 확인하십시오.

예제 airsim nodes는 ```rosrun airsim example_name.py```를 사용하여 실행할 수 있습니다. ```rostopic echo / example_name``` 을 실행하여 다른 터미널에서 노드의 출력을 볼 수 있습니다. 터미널에서 ```rostopic echo```를 입력 한 후 탭 완성을 통해 현재 게시 된 주제 목록을 볼 수 있습니다.
Rviz는 게시 된 데이터를 표시 할 수있는 유용한 시각화 도구입니다.

### 문제 해결

```rosrun airsim example_name.py```가 ```Couldn't find executable named...```를 반환하는 경우, 시스템에 이것은 실행 가능하다는 것을 알려주기 위해 ```chmod +x example_name.py```를 실행해야 합니다.


# C++ (coming soon)
