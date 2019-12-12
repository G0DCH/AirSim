# AirSim용 PX4 설정

[PX4 소프트웨어 스택](http://github.com/px4/firmware)은 광범위한 보드와 센서를 지원하고 미션 계획과 같은 고급 작업을 위한 내장 기능을 갖춘 널리 사용되는 오픈 소스 비행 컨트롤러입니다. 자세한 내용은 [px4.io] (http://px4.io)를 방문하십시오.

**주의** : AirSim의 모든 릴리스는 항상 PX4로 테스트되어 지원을 보장하지만, PX4 설정은 쉬운 일이 아닙니다. PX4 스택에 대한 중간 수준의 경험이 없으면 [Simple_flight] (simple_flight.md)를 사용하는 것이 좋습니다. 이는 현재 AirSim의 기본값입니다.

## 지원되는 하드웨어

다음 Pixhawk 하드웨어는 AirSim에서 테스트되었습니다.

1. [3DR Pixhawk v2](https://3dr.com/support/pixhawk/)
2. [3DR Pixhawk mini](https://store.3dr.com/products/3dr-pixhawk)
2. [Pixhawk PX4 2.4.8](http://www.banggood.com/Pixhawk-PX4-2_4_8-Flight-Controller-32-Bit-ARM-PX4FMU-PX4IO-Combo-for-Multicopters-p-1040416.html)
3. [PixFalcon](https://hobbyking.com/en_us/pixfalcon-micro-px4-autopilot.html?___store=en_us)
4. [PixRacer](https://www.banggood.com/Pixracer-Autopilot-Xracer-V1_0-Flight-Controller-Mini-PX4-Built-in-Wifi-For-FPV-Racing-RC-Multirotor-p-1056428.html?utm_source=google&utm_medium=cpc_ods&utm_content=starr&utm_campaign=Smlrfpv-ds-FPVracer&gclid=CjwKEAjw9MrIBRCr2LPek5-h8U0SJAD3jfhtbEfqhX4Lu94kPe88Zrr62A5qVgx-wRDBuUulGzHELRoCRVTw_wcB)
5. [Pixhawk 2.1](http://www.proficnc.com/) (PX4 Flight Stack 사용)

3DR Pixhawk Mini는 기본적으로 작동하며 다른 펌웨어는 최신 펌웨어로 re-flash해야 할 수도 있습니다.

## PX4 Hardware-in-Loop 설정

이를 위해 위에 나열된 지원 장치 중 하나가 필요합니다. 수동 비행의 경우 RC 와 송신기도 필요합니다.

1. RC 수신기가 RC 송신기와 연결되어 있는지 확인하십시오. RC 송신기를 비행 컨트롤러의 RC 포트에 연결하십시오. 자세한 내용은 RC 설명서 및 [PX4 문서](https://docs.px4.io/en/getting_started/rc_transmitter_receiver.html)를 참조하십시오.
2. [QGroundControl](http://qgroundcontrol.com/)을 다운로드하고 실행 한 후 비행 컨트롤러를 USB 포트에 연결하십시오.
3. QGroundControl을 사용하여 최신 PX4 Flight Stack을 플래시하십시오.
[초기 펌웨어 설정 비디오](https://dev.px4.io/starting-initial-config.html) 참조.
4. QGroundControl에서 HIL Quadrocopter X 기체를 선택하여 Pixhawk for HIL 시뮬레이션을 구성하십시오. PX4 재부팅 후 "HIL Quadrocopter X"가 선택되어 있는지 확인하십시오.
5. QGroundControl에서 라디오 탭으로 이동하여 보정하십시오 (리모콘이 켜져 있고 수신기에 바인딩 표시기가 있는지 확인하십시오).
6. 비행 모드 탭으로 이동하여 리모콘 스위치 중 하나를 "모드 채널"로 선택하십시오. 그런 다음 (예를 들어) 스위치의 두 위치에 대해 안정화 및 자세 비행 모드를 설정하십시오
7. QGroundControl의 튜닝 섹션으로 이동하여 적절한 값을 설정하십시오. 예를 들어, Fly Sky의 FS-TH9X 리모컨의 경우 다음 설정이 보다 사실적인 느낌을 줍니다: Hover Throttle = mid+1 mark, Roll and pitch sensitivity = mid-3 mark, Altitude and position control sensitivity = mid-2 mark.
8. [AirSim settings](settings.md) 파일에서 차량 구성에 PX4를 다음과 같이 지정하십시오.
```
{
  "SettingsVersion": 1.2,
  "SimMode": "Multirotor",
  "Vehicles": {
    "PX4": {
      "VehicleType": "PX4Multirotor"
    }
  }
}
```

위의 설정 후에 RC를 사용하여 AirSim을 비행 할 수 있습니다. 일반적으로 RC 스틱 2개를 내리고 안쪽으로 가져와 차량을 장착 할 수 있습니다. 초기 설정 후 QGroundControl이 필요하지 않습니다. 일반적으로 (수동 대신) 안정화 모드는 초보자에게 더 나은 경험을 제공합니다.

[Python API](apis.md)에서 드론을 제어 할 수도 있습니다.

이 문서의 모든 설정 단계를 보여주는 [Walkthrough 데모 비디오](https://youtu.be/HNWdYrtw3f0) 및 [Unreal AirSim 설정 비디오](https://youtu.be/1oY8Qu5maQQ)를 참조하십시오.

## PX4 Software-in-Loop 설정
PX4 SITL 모드에는 Pixhawk 또는 Pixracer와 같은 별도의 장치가 필요하지 않습니다. 실제로 PX4 팀의 시뮬레이터와 함께 PX4를 사용하는 것이 좋습니다. 그러나 이것은 실제로 설정하기가 더 어렵습니다. SITL 모드에서 PX4를 설정하려면 [전용 페이지](px4_sitl.md)를 참조하십시오.

## FAQ

#### 드론은 제대로 비행하지 않고, "미친 듯이" 돌아다닙니다.

이 문제를 일으킬 수 있는 몇 가지 이유가 있습니다. 먼저 시뮬레이터를 시작할 때 드론이 멀리 떨어지지 않도록 하십시오. 커스텀 Unreal 환경을 만들었고 플레이어 시작 지점이 지면 위에 너무 높은 곳에 있는 경우에 발생할 수 있습니다. 이 경우 PX4의 내부 조정이 혼동되는 것 같습니다.

또한 QGroundControl을 사용해야하며 QGroundControl을 올바르게 장착하고 이륙 할 수 있어야합니다.

마지막으로, 이는 드문 경우지만 기계 성능 문제 일 수 있습니다. [하드 드라이브 성능](hard_drive.md)를 확인하십시오.

#### Arducopter 또는 다른 MavLink 구현을 사용할 수 있습니까?

우리의 코드는 [PX4 펌웨어](https://dev.px4.io/)로 테스트되었습니다. 우리는 Arducopter 또는 다른 mavlink 구현을 테스트하지 않았습니다. 일부 비행 API는 MAV_CMD_DO_SET_MODE 메시지 (예 : PX4_CUSTOM_MAIN_MODE_AUTO)에서 PX4 사용자 지정 모드를 사용합니다.

#### Pixhawk 하드웨어를 찾지 못합니다.

settings.json 파일에서 "SerialPort":"*,115200" 줄을 확인하십시오. 여기서 별표는 "Pixhawk 장치처럼 보이는 직렬 포트를 찾지만 모든 유형의 Pixhawk 하드웨어에서 항상 작동하는 것은 아닙니다"를 의미합니다.
따라서 Windows에서는 장치 관리자를 사용하여 실제 COM 포트를 찾을 수 있습니다. "포트(COM & LPT)"에서 장치를 연결하고 새로운 COM 포트가 나타나는지 확인하십시오. "USB Serial Port(COM5)"라는 새 포트가 있다고 가정 해 봅시다.
그런 다음 SerialPort 설정을 "SerialPort":"COM5,115200"로 변경하십시오.

Linux에서 "usb-3D_Robotics_PX4_FMU_v2.x_0-if00"과 같은 장치 이름이 표시되면 해당 이름을 사용하여 연결할 수 있는 경우 "ls /dev/serial/by-id"를 실행하여 다음과 같이 장치를 찾을 수 있습니다 :
"SerialPort":"/dev/serial/by-id/usb-3D_Robotics_PX4_FMU_v2.x_0-if00". 이 긴 이름은 실제로 실제 이름에 대한 심볼릭 링크입니다. "ls -l ..."을 사용하는 경우 일반적으로 "/dev/ttyACM0"와 같은 해당 심볼릭 링크를 찾을 수 있습니다. 따라서 "SerialPort":"/dev/ttyACM0,115200"와 마찬가지로 동작합니다. 그러나 이 매핑은 windows와 유사하며 자동으로 할당되어 변경 될 수 있지만 실제 TTY 직렬 장치 매핑이 변경되더라도 긴 이름은 작동합니다.

#### WARN  [commander] Takeoff denied, disarm and re-try

이것은 PX4가 아직 원점 위치를 계산하지 않았을 때 이륙하려고 하면 발생합니다. GPS 신호에 만족하면 PX4가 홈 위치를 보고하고 다음 메시지가 표시됩니다:

```
INFO  [commander] home: 47.6414680, -122.1401672, 119.99
INFO  [tone_alarm] home_set
```

그러나 이 시점까지 PX4는 이륙 명령을 거부합니다.

#### 드론에게 무언가를 하도록 지시하면 항상 착륙합니다

예를 들어 DroneShell `moveToPosition -z -20 -x 50 -y 0`을 사용하지만, 목표 위치에 도달하면 드론이 착륙하기 시작합니다. 이것은 오프 보드 모드가 완료 될 때 PX4의 기본 동작입니다. 드론이 호버링 하도록 설정하려면 이 PX4 파라미터를 설정하십시오 :
```
param set COM_OBL_ACT 1
```

#### message length mismatches errors 가 발생합니다.
QGC의 MAV_PROTO_VER 매개 변수를 "Always use version 1"로 설정해야합니다. 자세한 내용은 [이 이슈](https://github.com/Microsoft/AirSim/issues/546)를 참조하십시오.
