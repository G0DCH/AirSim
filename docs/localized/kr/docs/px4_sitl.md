# PX4 Software-in-Loop 설정

[PX4](http://dev.px4.io) 소프트웨어는 Linux에서 실행되는 스택의 "SITL(Software-in-loop) 시뮬레이션" 버전을 제공합니다. Windows에서는 실행되지 않지만, [BashOnWindows](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide)를 설치하면 빌드하고 실행할 수 있습니다.

1. Linux bash 터미널에서 [Linux의 다음 단계](http://dev.px4.io/starting-installing-linux.html)를 따르고 `NuttX 기반 하드웨어`의 지시 사항을 **모두** 따라 전제 조건을 설치하십시오. 우리는 또한 정확히 필요한 것에 대해 좀 더 간결한 [PX4 빌드 지침](px4_build.md) 사본을 포함 시켰습니다.

2. PX4 소스 코드를 얻고 posix SITL 버전의 PX4를 빌드하십시오:
    ```
    mkdir -p PX4
    cd PX4
    git clone https://github.com/PX4/Firmware.git
    cd Firmware
    git checkout v1.8.2  # Pick a well known "good" release tag.
    ```
3. SITL 모드에서 PX4 펌웨어를 빌드하고 시작하려면 다음 명령을 사용하십시오:
    ```
    make posix_sitl_ekf2  none_iris
    ```
4. `INFO  [simulator] Waiting for initial data on UDP port 14560`과 같은 메시지가 표시되어야 합니다. 이는 SITL PX4 앱이 누군가의 연결을 기다리고 있음을 의미합니다.
5. 이제 [AirSim settings](settings.md) 파일을 편집하여 다음이 있는지 확인하십시오.
    ---json
    {
        "SettingsVersion": 1.2,
        "SimMode": "Multirotor",
        "Vehicles": {
            "PX4": {
                "VehicleType": "PX4Multirotor",
                "UseSerial": false
            }
        }
    }
}

    ---
6. Unreal 환경을 실행하고 UDP를 통해 SITL에 연결해야합니다. SITL PX4 창에서 `[mavlink]` 및 `[commander]` 등의 메시지가 많이 나타납니다.
7. 비행 컨트롤러 하드웨어와 마찬가지로 QGroundControl을 사용할 수도 있습니다. 물리적 보드가 없으므로 RC를 직접 연결할 수 없습니다. 따라서 대안은 XBox 360 컨트롤러를 사용하거나 USB(예: FrSky Taranis X9D Plus의 경우)를 사용하거나 트레이너 USB 케이블을 PC에 사용하여 RC를 연결하는 것입니다. 그러면 RC가 조이스틱처럼 보입니다. RC 제어에 가상 조이스틱을 사용하려면 QGroundControl에서 추가 설정을 수행해야합니다.

## GPS 원점 설정

홈 위치가 정확하도록 PX4 SITL 모드를 구성해야 합니다. 원본이 AirSim AVehiclePawnBase::HomeLatitude 및 HomeLongitude에 설정된 것과 일치하도록 PX4 콘솔 창에서 다음을 실행하십시오.

````
param set LPE_LAT 47.641468
param set LPE_LON -122.140165
````

또한 각 오프 보드 제어 명령 후 드론이 자동으로 호버링 하도록 이 설정을 지정할 수도 있습니다(기본 설정은 착륙).

````
param set COM_OBL_ACT 1
````

이제 Unreal 앱을 닫고 `px4` 앱을 다시 시작한 다음 Unreal 앱을 다시 시작하십시오.  In fact, every time you stop the unreal app you have top restart the `px4` app.

## 홈 위치 확인

DroneShell을 사용하여 명령(arm, 이륙 등)을 실행하는 경우 홈 위치가 설정 될 때까지 기다려야 합니다. PX4 SITL 콘솔이 이 메시지를 출력 한 것을 볼 수 있습니다:

````
INFO  [commander] home: 47.6414680, -122.1401672, 119.99
INFO  [tone_alarm] home_set
````

이제 DroneShell 'pos' 명령이 이 위치를 보고해야하며 PX4에서 명령을 승인해야합니다. 홈 포지션 없이 이륙을 시도하면 다음과 같은 메시지가 나타납니다.

````
WARN  [commander] Takeoff denied, disarm and re-try
````

원점 위치가 설정되면 'pos' 명령으로 보고 된 로컬 위치를 확인하십시오:

````
Local position: x=-0.0326988, y=0.00656854, z=5.48506
````

z 좌표가 이와 같이 크면 이륙이 예상대로 작동하지 않을 수 있습니다. SITL 및 시뮬레이션을 재설정하면 해당 문제가 해결됩니다.

## 리모콘 없음

예를 들어 DroneShell 명령을 사용하여 원격 제어없이 비행하려는 경우 이동 명령이 완료 될 때마다 PX4가 "failsafe mode on"을 트리거하지 않도록 다음 매개 변수를 설정해야합니다.

````
param set NAV_RCL_ACT 0
param set NAV_DLL_ACT 0
````

참고 :이 안전 장치 없이 비행하기에는 너무 위험하므로 실제 무인 항공기에서는 이 작업을 수행하지 `마십시오`.

## VirtualBox Ubuntu 사용

`VirtualBox Ubuntu` 머신에서 위의 posix_sitl을 실행하려면 localhost와 다른 IP 주소를 갖습니다. 따라서 이 경우 [설정 파일](settings.md)을 편집하고 UdpIp 및 SitlIp를 가상 머신의 IP 주소로 변경해야 합니다.
LocalIpAddress를 Unreal 엔진을 실행하는 호스트 머신의 주소로 설정하십시오. 

## 원격 컨트롤러

리모컨이나 xbox 게임 패드와 같은 조이스틱을 사용하여 시뮬레이션 드론을 비행하는 데는 몇 가지 옵션이 있습니다. [원격 컨트롤러](remote_control.md#RC_Setup_for_PX4) 참조

