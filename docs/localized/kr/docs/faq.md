
# FAQ

## 일반
#### Unreal 에디터가 활성 윈도우가 아닐 때 느립니다

편집/편집기 환경 설정으로 이동하여 "모든 설정"을 선택하고 검색창에 "CPU"를 입력하십시오.
"Use Less CPU when in Background"이라는 제목의 설정이 있으며 이 체크박스의 선택을 해제하세요.

#### Unreal에서 마우스가 사라집니다

그렇습니다, Unreal은 마우스를 숨기고, 우리는 마우스를 그리지 않습니다. 마우스를 다시 가져 오려면 Alt+TAB을 사용하여 다른 창으로 전환하십시오. 이를 완전히 피하려면 Unreal 에디터의 프로젝트 설정으로 이동하여 입력 탭으로 이동하여 마우스 캡처에 대한 모든 설정을 비활성화하십시오.

#### 설정 파일은 어디에 있으며 어떻게 수정합니까?
AirSim은 `~/Documents/AirSim/settings.json`에 빈 설정 파일을 생성합니다. 사용 가능한 [설정 옵션](settings.md)을 볼 수 있습니다. 

#### 드론을 어떻게 설치합니까?
simple_flight를 사용하는 경우 차량이 이미 준비되어 있고 비행 할 준비가 되어있습니다. PX4의 경우 리모컨의 양쪽 스틱을 아래로 잡고 중앙으로 고정하여 설치 할 수 있습니다.

#### API 호출시 오류가 발생합니다
이 오류가 발생하면,
```
TypeError: unsupported operand type(s) for *: 'AsyncIOLoop' and 'float'
```
아마도 토네이도 패키지 버전이 5.0 미만이 필요한 `msgpack-rpc-python`과 충돌하는 파이썬에서 버전이 5.0 이상인 토네이도 패키지의 업그레이드 버전 때문일 것입니다. 이 문제를 해결하려면 다음과 같이 패키지를 업데이트 하십시오:
```
pip install --upgrade msgpack-rpc-python
```
그러나 이것은 새로운 토네이도를 제거하고 오래된 토네이도를 다시 설치하기 때문에 무언가(예: PyTorch 0.4 이상)를 손상시킬 수 있습니다. 이를 피하려면 새로운 [콘다 환경](https://conda.io/docs/user-guide/tasks/manage-environments.html)을 만들어야 합니다.

#### Unreal 프로젝트를 컴파일 할 때 Eigen을 찾을 수 없다는 오류가 발생합니다.
AirSim이 빌드되지 않았고 플러그인 폴더가 Unreal 프로젝트 폴더에 복사 되었기 때문일 수 있습니다. 이 문제를 해결하려면 먼저 [AirSim을 빌드](build_windows.md)해야 합니다(Windows에서 `build.cmd`를 실행하십시오).

#### 문제가 발생했습니다. 어떻게 디버깅합니까?

먼저 예외 창에서 C++ 예외를 켜십시오.

![exceptions](images/exceptions.png)

실행 중 표시되는 모든 예외(예: there might be an initial exception from VSPerf140 that you can ignore)의 스택 추적을 복사 한 다음 이러한 호출 스택을 새로운 AirSim GitHub 이슈에 붙여 넣습니다.

#### 세그멘테이션 뷰에서 색상의 의미는 무엇입니까?

카메라 뷰 및 변경 방법에 대한 정보는 [카메라 뷰](camera_views.md)를 참조하십시오.

#### Unreal 4.xx는 4.yy만큼 좋지 않습니다

Unreal 4.15는 Foliage 머티리얼에서 'Dithered LOD Transition' 체크 박스를 선택 해제하여 Foliage LOD dithering을 사례별로 비활성화 할 수 있는 기능을 추가했습니다. dithered LOD transitions이 작동하려면 모든 LOD에 사용된 모든 머티리얼의 체크박스를 체크해야 합니다. 체크하면 생성된 foliage의 전환이 훨씬 매끄럽고 4.14보다 좋아 보입니다.

#### XBox 컨트롤러를 사용하여 비행 할 수 있습니까?
자세한 내용은 [XBox 컨트롤러](xbox_controller.md)를 참조하십시오.

#### AirSim으로 헥사콥터를 만들 수 있습니까?
[헥사콥터 제작법](https://github.com/microsoft/airsim/wiki/hexacopter)을 참조하십시오.

#### AirSim을 여러 대의 차량에 어떻게 사용합니까?
[다중 차량 설정 가이드](multi_vehicle.md)를 참조하십시오.

#### 어떤 컴퓨터가 필요합니까?
Unreal 환경의 크기에 따라 다릅니다. AirSim과 함께 제공되는 Blocks 환경은 매우 기본적이고 일반적인 랩톱에서 작동합니다. 우리가 연구에 사용하는 [Modular Neighborhood Pack](https://www.unrealengine.com/marketplace/modular-neighborhood-pack)에는 최소 4GB의 RAM이있는 GPU가 필요합니다. [Open World environment](https://www.unrealengine.com/marketplace/open-world-demo-collection)에는 8GB RAM의 GPU가 필요합니다. 일반적인 개발 시스템에는 32GB의 RAM과 NVIDIA TitanX 및 [고속 하드 드라이브](hard_drive.md)가 필요합니다.

#### 문제는 어떻게 보고합니까?
아래와 같이 구성을 포함하는 것이 좋습니다. 로그도 포함 할 수 있으면 조사가 더 빨라질 수 있습니다.

````
Operating System: Windows 10 64bit
CPU: Intel Core i7
GPU: Nvidia GTX 1080
RAM: 32 GB
Flight Controller: Pixhawk v2
Remote Control: Futaba
````

기본 `~/Document/AirSim/settings.json`을 수정 한 경우 설정도 포함하십시오.

PX4를 사용하는 경우 [MavLink 또는 PX4에서 로그 캡처](px4_logging.md)를 시도하십시오.

[GitHub 이슈](https://github.com/microsoft/airsim/issues)를 통해 문제를 제기하십시오.

## Others

[Linux Build FAQ](build_linux.md#faq)
[Windows Build FAQ](build_windows.md#faq)
[PX4 Setup FAQ](px4_setup.md#faq)
[Remote Control FAQ](remote_control.md#faq)
[Unreal Blocks Environment FAQ](remote_control.md#faq)
[Unreal Custom Environment FAQ](remote_control.md#faq)
