# 바이너리 다운로드

미리 컴파일된 바이너리를 다운로드하고 실행하면 바로 시작할 수 있습니다. 고유한 Unreal 환경을 설정하려면 [이 지침](https://github.com/Microsoft/AirSim/#how-to-get-it)을 참조하십시오.

### Unreal Engine

**Windows**: [최신 릴리스](https://github.com/Microsoft/AirSim/releases)에서 선택한 환경에 대한 바이너리를 다운로드하십시오.

**Linux**: Ubuntu 16.04 LTS 용 바이너리가 곧 출시 될 예정입니다. 지금은 [Linux로 빌드](build_linux.md)해야합니다.

### Unity (실험적 릴리스)
[Unity Asset Store](https://assetstore.unity.com/)에서는 AirSim on Unity의 실험적 릴리스로 Windridge City라는 무료 환경을 이용할 수 있습니다. 이 작업은 진행 중이며 모든 기능이 아직 작동하지 않을 수 있다는 점에 유의하십시오.

## 차량 제어
대부분의 사용자는 일반적으로 차량을 제어하기 위해 [API](apis.md)를 사용합니다. 그러나 수동으로 차량을 제어 할 수도 있습니다. 키보드, 게임 패드 또는 [스티어링 휠](steering_wheel_installation.md)을 사용하여 자동차를 운전할 수 있습니다. 드론을 수동으로 비행하려면 XBox 컨트롤러 또는 원격 컨트롤러가 필요합니다(키보드 지원에 부담없이 [기여](../CONTRIBUTING.md)하세요). 자세한 사항은 [원격 제어 설정](remote_control.md)을 참조하십시오. 또는 프로그래밍 방식으로 [API](apis.md)를 사용하거나 키보드를 사용하여 소위 [Computer Vision 모드](image_apis.md)를 사용하여 주변 환경을 이동할 수 있습니다.

## 좋은 GPU가 없습니까?
CityEnviron과 같은 AirSim 바이너리는 원활하게 실행하려면 강력한 GPU가 필요합니다. Windows에서 `run.bat` 파일을 다음과 같이 편집하여 저해상도 모드로 실행할 수 있습니다:
```
start CityEnviron -ResX=640 -ResY=480 -windowed
```

