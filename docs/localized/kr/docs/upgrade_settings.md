# 설정 업그레이드

더욱 유연하고 깔끔한 인터페이스를 위해 AirSim 1.2의 설정 스키마가 변경되었습니다. 오래된 [settings.json](settings.md) 파일이 있는 경우 파일을 삭제하고 AirSim을 다시 시작하거나 이 안내서를 사용하여 수동으로 업그레이드 할 수 있습니다.

## 빠른 방법
[settings.json](settings.md)을 삭제하고 필요한 설정을 다시 추가하는 것이 좋습니다.
사용 가능한 설정에 대한 자세한 내용은 [문서](settings.md)를 참조하십시오.

## 변경점

### UsageScenario
이전에는 `UsageScenario`를 사용하여 `ComputerVision` 모드를 지정했습니다. 이제 대신 `"SimMode":"ComputerVision"`을 사용합니다.

### 카메라 기본값 및 카메라 설정 변경
이전에는 루트에 `CaptureSettings`와 `NoiseSettings`가 있었습니다. 이제 이것들은 새로운 `CameraDefaults` 요소에 결합되었습니다. [이 요소의 스키마](settings.md#camera_settings)는 나중에 차량에서 카메라를 구성하는데 사용됩니다.

### Gimbal
[Gimbal 요소](settings.md # Gimbal)(이전 Gimble 요소 대신)가 이제 `CaptureSettings`에서 제외되었습니다.

### CameraID에서 CameraName
모든 설정은 이제 ID 대신 [이름](image_apis.md#available_cameras)으로 카메라를 참조합니다.

### PX4 사용
새로운 Vehicles 요소를 사용하면 만들 차량을 지정할 수 있습니다. PX4를 사용하려면 [이 섹션](settings.md#using_px4)을 참조하십시오.

### AdditionalCameras
이전 `AdditionalCameras` 설정이 이제 차량 설정 내에서 [카메라 요소](settings.md#Common_Vehicle_Setting)로 대체되었습니다.

