# 원격 조종

수동으로 비행하려면 리모컨 또는 RC가 필요합니다. 없는 경우 [API](apis.md)를 사용하여 프로그래밍 방식으로 비행하거나 소위 [Computer Vision mode](image_apis.md)를 사용하여 키보드를 사용하여 이동할 수 있습니다.

## 기본 구성을 위한 RC 설정

기본적으로 AirSim은 [simple_flight](simple_flight.md)를 비행 컨트롤러로 사용하여 USB 포트를 통해 RC를 컴퓨터에 연결합니다.

XBox 컨트롤러 또는 [FrSky Taranis X9D Plus](https://hobbyking.com/en_us/frsky-2-4ghz-accst-taranis-x9d-plus-and-x8r-combo-digital-telemetry-radio-system-mode-2.html)를 사용할 수 있습니다. XBox 360 컨트롤러는 정확하지 않으며 실제 경험을 원한다면 권장하지 않습니다. 작동하지 않는 경우 아래 FAQ를 참조하십시오.

 ### 다른 장치들
 AirSim은 매우 다양한 장치를 감지할 수 있지만, 상기 장치 이외의 장치에는 *추가 구성*이 필요할 수 있습니다. 앞으로 settings.json을 통해 이 구성을 설정하는 기능을 추가 할 것입니다. 지금까지 문제가 해결되지 않으면 [x360ce](http://www.x360ce.com/)와 같은 해결 방법을 시도하거나 [SimJoystick.cpp 파일](/Unreal/Plugins/AirSim/Source/SimJoyStick/SimJoyStick.cpp#L50)의 코드 변경을 원할 수 있습니다.

 ### FrSky Taranis X9D Plus에 대한 참고 사항

 [FrSky Taranis X9D Plus](https://hobbyking.com/en_us/frsky-2-4ghz-accst-taranis-x9d-plus-and-x8r-combo-digital-telemetry-radio-system-mode-2.html)는 USB 포트가 있어 PC에 직접 연결할 수 있다는 장점이 있는 실제 UAV 리모콘입니다. [AirSim 구성 파일을 다운로드](misc/AirSim_FrSkyTaranis.bin)하고 [이 튜토리얼을 따라](https://www.youtube.com/watch?v=qe-13Gyb0sw) RC로 가져올 수 있습니다. 그러면 모든 채널이 올바르게 구성된 RC에서 "sim"모델이 표시되어야 합니다.

### Linux에 대한 참고 사항
Linux에서 현재 기본 구성은 Xbox 컨트롤러를 사용하는 것입니다. 이것은 다른 장치가 제대로 작동하지 않을 수 있음을 의미합니다. 앞으로 settings.json에서 RC를 구성하는 기능을 추가 할 예정이지만 지금은 다른 장치를 사용하기 위해 [SimJoystick.cpp 파일](/Unreal/Plugins/AirSim/Source/SimJoyStick/SimJoyStick.cpp#L340)의 코드를 *변경해야* 할 수도 있습니다.

## PX4에서 RC 설정

AirSim은 PX4 비행 컨트롤러를 지원하지만 다른 설정이 필요합니다. 쿼드 로터와 함께 사용할 수 있는 많은 리모콘 옵션이 있습니다. FrSky Taranis X9D Plus, FlySky FS-TH9X 및 Futaba 14SG는 AirSim에서 잘 동작했습니다. RC를 구성하는 하이 레벨 단계는 다음과 같습니다.

1. Hardware-in-Loop 모드를 사용하려면 특정 브랜드의 RC 용 송신기가 필요합니다. 이 정보는 RC 사용 설명서에서 찾을 수 있습니다. 
2. Hardware-in-Loop 모드의 경우 송신기를 Pixhawk에 연결합니다. 일반적으로 온라인 문서 또는 YouTube 비디오 자습서를 참조하십시오.
3. [QGroundControl에서 RC 교정](https://docs.qgroundcontrol.com/en/SetupView/Radio.html).

자세한 내용은 [PX4 RC 구성](https://docs.px4.io/en/getting_started/rc_transmitter_receiver.html) 및 [안내서](http://ardupilot.org/copter/docs/common-pixhawk-and-px4-compatible-rc-transmitter-and-receiver-systems.html)를 참조하십시오.

### XBox 360 USB 게임 패드 사용

SITL 모드에서 xbox 컨트롤러를 사용할 수도 있습니다. 실제 RC 컨트롤러만큼 정확하지는 않습니다.
설정 방법에 대한 자세한 내용은 [xbox controller](xbox_controller.md)를 참조하십시오.

### Playstation 3 컨트롤러 사용

Playstation 3 컨트롤러가 AirSim 컨트롤러로 작동하는 것으로 확인되었습니다. 그러나 Windows에서는 Xbox 360 컨트롤러처럼 보이도록하는 에뮬레이터가 필요합니다. 예를 들어 [x360ce Xbox 360 컨트롤러 에뮬레이터](https://github.com/x360ce/x360ce)와 같은 다양한 솔루션이 온라인에서 제공됩니다.

### DJI 컨트롤러

Nils Tijtgat는 [DJI 컨트롤러와 AirSim을 함께 사용하는 방법](https://timebutt.github.io/static/using-a-phantom-dji-controller-in-airsim/)에 대한 훌륭한 블로그를 작성했습니다.

## FAQ

#### 기본 구성을 사용하고 있으며 AirSim은 RC가 USB에서 감지되지 않는다고 말합니다.

이는 일반적으로 여러 RC 및 XBox/Playstation 게임 패드 등이 연결된 경우에 발생합니다. Windows에서 Windows + S 키를 누르고 "USB 게임 컨트롤러 설정"을 검색하십시오(이전 버전의 Windows에서는 "joystick"을 시도하십시오). PC에 연결된 모든 게임 컨트롤러가 표시됩니다. 당신의 것이 보이지 않는다면, Windows에서 이를 감지하지 못한 것이기 때문에 먼저 문제를 해결해야 합니다. 당신의 것이 보이지만 리스트의 상단에 없는 경우(예 : 인덱스 0), AirSim은 기본적으로 인덱스 0에 있는 RC를 사용하려고 하기 때문에 AirSim에 알려야 합니다. 이렇게 하려면 `~/Documents/AirSim` 폴더로 이동하여 `settings.json`을 열고 다음 설정을 추가/수정하십시오. 아래는 AirSim이 인덱스 = 2에서 RC를 사용하도록 지시합니다.
```
{
    "SettingsVersion": 1.2,
    "SimMode": "Multirotor",
    "Vehicles": {
        "SimpleFlight": {
            "VehicleType": "SimpleFlight",
            "RC": {
              "RemoteControlID": 2
            }
        }
    }
}
```

#### XBox/PS3 컨트롤러를 사용할 때 차량이 불안정 해 보입니다

일반 게임 패드는 그다지 정확하지 않으며 많은 노이즈가 있습니다. 대부분의 경우 상당한 오프셋이 나타날 수도 있습니다(즉, 스틱이 0일 때 출력이 0이 아님). 따라서 해당 동작이 예상됩니다.

#### AirSim의 RC 보정은 어디에 있습니까?

아직 구현하지 않았습니다. 즉, 현재 RC 펌웨어에서 보정을 수행할 수 있는 기능이 있어야 합니다.

#### RC가 PX4 설정에서 작동하지 않습니다.

먼저 RC가 [QGroundControl](https://docs.qgroundcontrol.com/en/SetupView/Radio.html)에서 작동하는지 확인하려고 합니다. 작동하지 않는다면 AirSim에서 작동하지 않을 것 입니다. PX4 모드는 PX4와 관련된 다양한 문제를 다루는 중급 수준 이상의 경험이 있는 사람에게 적합하며 일반적으로 PX4 포럼의 도움을 받도록 권장합니다.