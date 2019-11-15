# AirSim API

## 서론
AirSim은 API를 제공하므로 프로그래밍 방식으로 시뮬레이션에서 차량과 상호 작용할 수 있습니다. 이 API를 사용하여 이미지 검색, 상태 가져 오기, 차량 제어 등을 수행 할 수 있습니다.

## Python을 이용한 빠른 시작
Python을 사용하여 AirSim API를 호출하려면 Anaconda를 Python 3.5 이상 버전과 함께 사용하는 것이 좋지만 일부 코드는 Python 2.7에서도 작동 할 수 있습니다(호환성 개선을 위해 [도움을 주세요](../CONTRIBUTING.md)!).

먼저 이 패키지를 설치하십시오 :

```
pip install msgpack-rpc-python
```

[릴리스](https://github.com/Microsoft/AirSim/releases)에서 AirSim 바이너리를 가져 오거나 소스 ([Windows](build_windows.md), [Linux](build_linux.md))에서 컴파일 할 수 있습니다. AirSim이 실행 된다면, 차량으로 자동차를 선택한 다음 `PythonClient\car\` 폴더로 이동하여 다음을 실행하십시오.

```
python hello_car.py
```

Visual Studio 2017을 사용하는 경우 AirSim.sln을 열고, PythonClient를 시작 프로젝트로 설정하고 시작 스크립트로 `car\hello_car.py`를 선택하십시오.

### AirSim 패키지 설치
아래와 같이 간단하게 `airsim` 패키지를 설치할 수도 있습니다.

```
pip install airsim
```

이 패키지의 소스 코드와 샘플은 리포지토리의 `PythonClient` 폴더에서 찾을 수 있습니다.

**주의**
1. 예제 폴더에`setup_path.py` 파일이 있을 수도 있습니다. 이 파일에는 부모 폴더에서 `airsim` 패키지를 사용할 수 있는지 감지하는 간단한 코드가 있으며 이 경우 pip installed 패키지 대신 이를 사용하므로 항상 최신 코드를 사용합니다.
2. AirSim은 아직 개발 중이므로 새로운 API를 사용하려면 패키지를 자주 업데이트해야 할 수도 있습니다.

## C++ 사용자
C ++ API 및 예제를 사용하려면 [C++ API 안내서](apis_cpp.md)를 참조하십시오.

## Hello Car
Python을 사용하여 AirSim API를 통해 시뮬레이션 자동차를 제어하는 ​​방법은 다음과 같습니다 ([C++ 예제](apis_cpp.md#hello_car) 또한 참조하세요):

```python
# 예제를 실행할 준비: PythonClient/car/hello_car.py
import airsim
import time

# AirSim 시뮬레이터에 연결
client = airsim.CarClient()
client.confirmConnection()
client.enableApiControl(True)
car_controls = airsim.CarControls()

while True:
    # 차의 상태를 가져옴
    car_state = client.getCarState()
    print("Speed %d, Gear %d" % (car_state.speed, car_state.gear))

    # 차에 대한 컨트롤 설정
    car_controls.throttle = 1
    car_controls.steering = 1
    client.setCarControls(car_controls)

    # 차를 약간 운전하게 함
    time.sleep(1)

    # 차로부터 카메라 이미지를 얻어옴
    responses = client.simGetImages([
        airsim.ImageRequest(0, airsim.ImageType.DepthVis),
        airsim.ImageRequest(1, airsim.ImageType.DepthPlanner, True)]) 
    print('Retrieved images: %d', len(responses))

    # 이미지로 무언가를 함
    for response in responses:
        if response.pixels_as_float:
            print("Type %d, size %d" % (response.image_type, len(response.image_data_float)))
            airsim.write_pfm('py1.pfm', airsim.get_pfm_array(response))
        else:
            print("Type %d, size %d" % (response.image_type, len(response.image_data_uint8)))
            airsim.write_file('py1.png', response.image_data_uint8)

```

## Hello Drone
Python을 사용하여 AirSim API를 사용하여 시뮬레이션 쿼드 로터를 제어하는 ​​방법은 다음과 같습니다([C++ 예제](apis_cpp.md#hello_drone) 또한 참조하세요):

```python
# 예제를 실행할 준비: PythonClient/multirotor/hello_drone.py
import airsim

# AirSim 시뮬레이터에 연결
client = airsim.MultirotorClient()
client.confirmConnection()
client.enableApiControl(True)
client.armDisarm(True)

# 비동기 메소드는 미래에 return합니다. 작업이 완료 될 때까지 join()을 호출하십시오.
client.takeoffAsync().join()
client.moveToPositionAsync(-10, 10, -10, 5).join()

# 이미지를 찍음
responses = client.simGetImages([
    airsim.ImageRequest("0", airsim.ImageType.DepthVis), 
    airsim.ImageRequest("1", airsim.ImageType.DepthPlanner, True)])
print('Retrieved images: %d', len(responses))

# 이미지로 무언가를 함
for response in responses:
    if response.pixels_as_float:
        print("Type %d, size %d" % (response.image_type, len(response.image_data_float)))
        airsim.write_pfm(os.path.normpath('/temp/py1.pfm'), airsim.getPfmArray(response))
    else:
        print("Type %d, size %d" % (response.image_type, len(response.image_data_uint8)))
        airsim.write_file(os.path.normpath('/temp/py1.png'), response.image_data_uint8)
```

## 일반적인 API

* `reset`: 차량을 원래 시작 상태로 리셋합니다. `reset` 호출 후 `enableApiControl` 및 `armDisarm`을 다시 호출해야 합니다.
* `confirmConnection`: 1초마다 연결 상태를 확인하고 콘솔에 보고하여 사용자가 연결 진행률을 볼 수 있도록 합니다.
* `enableApiControl`: (보통 시뮬레이터에서 RC 또는 조이스틱을 통한 경우)안전상의 이유로 기본적으로 자율 주행 차량에 대한 API 제어가 활성화되어 있지 않으며 사람 조작자가 전체 제어 권한을 갖습니다. 클라이언트는 API를 통해 제어를 요청하기 위해 이 호출을 해야 합니다. 차량의 운전자가 API 제어를 허용하지 않았을 가능성이 높습니다. 이는 enableApiControl이 영향을 미치지 않음을 의미합니다. 이것은 `isApiControlEnabled`로 확인할 수 있습니다.
* `isApiControlEnabled`: API 제어가 설정되면 true를 리턴합니다. false(기본값) 인 경우 API 호출이 무시됩니다. `enableApiControl`을 성공적으로 호출 한 후, isApiControlEnabled는 true를 반환해야 합니다.
* `ping`: 연결이 설정되면 이 호출은 true를 리턴하고 그렇지 않으면 타임 아웃까지 차단됩니다.
* `simPrintLogMessage`: 시뮬레이터 창에 지정된 메시지를 출력합니다. message_param도 제공되면 메시지 옆에 출력되며, 이 경우에 이 API가 동일한 메시지 값으로 호출되지만 다른 message_param으로 다시 호출되면 API는 표시 줄에 새 줄을 만드는 대신 이전 줄을 새 줄로 덮어 씁니다. 예를 들어, `simPrintLogMessage ( "Iteration :", to_string (i))`는 API가 다른 i 값으로 호출 될 때 디스플레이에서 동일한 행을 계속 업데이트합니다. severity 매개 변수의 유효 값은 다른 색상에 대응하는 0 ~ 3입니다.
* `simGetObjectPose`, `simSetObjectPose`: Unreal 환경에서 지정된 오브젝트의 포즈를 가져오고 설정합니다. 여기서 오브젝트는 언리얼 용어에서 "액터"를 의미합니다. 액터는 이름과 태그로 검색할 수 있습니다. UE 에디터에 표시된 이름은 각 실행에서 *자동 생성*되며 영구적이지 않습니다. 따라서 액터를 이름으로 참조하려면 UE Editor에서 자동 생성 된 이름을 변경해야합니다. 또는 Unreal Editor에서 해당 액터를 클릭 한 다음 [Tags property](https://answers.unrealengine.com/questions/543807/whats-the-difference-between-tag-and-tag.html)로 이동하여 "+"부호를 클릭하고 문자열 값을 추가하여 액터에 태그를 추가 할 수 있습니다. 여러 액터에 동일한 태그가 있으면 첫 번째 일치 항목이 반환됩니다. 일치하는 것이 없으면 NaN 포즈가 반환됩니다. 반환된 포즈는 플레이어 시작 시 원본과 함께 SI 단위의 NED 좌표에 있습니다. `simSetObjectPose`의 경우, 지정된 액터에 [Mobility](https://docs.unrealengine.com/en-us/Engine/Actors/Mobility)가 Movable로 설정되어 있어야합니다. 그렇지 않으면 정의되지 않은 동작이 발생합니다. `simSetObjectPose`는 `teleport` 매개 변수를 가지고 있는데, 이것은 객체가 다른 방식으로 [다른 객체를 통해 이동 됨](https://www.unrealengine.com/en-US/blog/moving-physical-objects)을 의미하며 이동이 성공하면 true를 반환합니다.

### 이미지 / Computer Vision API
AirSim은 깊이, 시차, 표면 법선 및 비전을 포함한 지상의 상태와 함께 여러 카메라에서 동기화 된 이미지를 검색하는 포괄적인 이미지 API를 제공합니다. [settings.json](settings.md)에서 해상도, FOV, 모션 블러 등의 파라미터를 설정할 수 있습니다. 충돌 상태를 감지하기위한 API도 있습니다. 카메라 플랜에 대한 정규화, 시차 이미지의 계산 및 [pfm 형식](pfm.md)으로 저장하여 지정된 수의 스테레오 이미지와 지상 깊이 상태를 생성하는 [완전한 코드](https://github.com/Microsoft/AirSim/tree/master/Examples/DataCollection/StereoImageGenerator.hpp)도 참조하십시오.

[이미지 API 및 컴퓨터 비전 모드](image_apis.md)에 대한 추가 정보

### 일시정지와 계속하기 APIs
AirSim은 `pause (is_paused)` API를 통해 시뮬레이션을 일시정지하고 계속할 수 있습니다. 시뮬레이션을 일시정지 하기 위해서 `pause (True)`를 호출하고 계속하기 위해서 `pause (False)`를 호출합니다. 특히 강화 학습을 사용하는 동안 지정된 시간 동안 시뮬레이션을 실행 한 다음 자동으로 일시 중지하는 시나리오가 있을 수 있습니다. 시뮬레이션이 일시 정지 된 동안 비용이 많이 드는 계산을 수행하고 새 명령을 보낸 다음 지정된 시간 동안 시뮬레이션을 다시 실행할 수 있습니다. 이것은 `continueForTime(seconds)` API에 의해 달성 될 수 있습니다. 이 API는 지정된 시간(초)동안 시뮬레이션을 실행 한 다음 시뮬레이션을 일시 중지합니다. 사용 예제는 [pause_continue_car.py](https://github.com/Microsoft/AirSim/tree/master/PythonClient//car/pause_continue_car.py) 및 [pause_continue_drone.py](https://github.com/Microsoft/AirSim/tree/master/PythonClient//multirotor/pause_continue_drone.py)를 참조하십시오.


### 충돌 API
충돌 정보는`simGetCollisionInfo` API를 사용하여 얻을 수 있습니다. 이 호출은 충돌 발생 여부뿐만 아니라 충돌 위치, 표면 법선, 침투 깊이 등의 정보가 있는 구조체를 반환합니다.

### Time of Day API
AirSim은 [ADirectionalLight 액터](https://github.com/Microsoft/AirSim/blob/master/Unreal/Plugins/AirSim/Source/SimMode/SimModeBase.cpp#L156)를 사용하는 환경에 `EngineSky/BP_Sky_Sphere` 클래스의 천구(sky sphere)가 있다고 가정합니다. 기본적으로 씬에서 태양의 위치는 시간과 함께 움직이지 않습니다. [설정](settings.md#timeofday)을 사용하여 AirSim이 장면에서 태양의 위치를 ​​계산하는 데 사용하는 위도, 경도, 날짜 및 시간을 설정할 수 있습니다.

다음 API 호출을 사용하여 주어진 날짜 시간에 따라 태양 위치를 설정할 수도 있습니다:

```
simSetTimeOfDay(self, is_enabled, start_datetime = "", is_start_datetime_dst = False, celestial_clock_speed = 1, update_interval_secs = 60, move_sun = True)
```

시간 효과를 사용하려면 `is_enabled` 매개 변수가 `True`이어야 합니다. `false`이면 태양 위치가 환경에서 원래 위치(its original)로 재설정됩니다.

다른 파라미터는 [설정](settings.md#timeofday)에서와 동일합니다.

### 날씨 API
기본적으로 모든 날씨 효과는 비활성화되어 있습니다. 날씨 효과를 사용하려면, 먼저 이것을 호출하십시오:

```
simEnableWeather(True)
```

예를 들어, 날씨 매개 변수를 취하는 'simSetWeatherParameter' 메소드를 사용하여 다양한 날씨 효과를 사용할 수 있습니다.

```
client.simSetWeatherParameter(airsim.WeatherParameter.Rain, 0.25);
```
두 번째 매개 변수 값은 0에서 1입니다. 첫 번째 매개 변수는 다음 옵션을 제공합니다:

```
class WeatherParameter:
    Rain = 0
    Roadwetness = 1
    Snow = 2
    RoadSnow = 3
    MapleLeaf = 4
    RoadLeaf = 5
    Dust = 6
    Fog = 7
```

`Roadwetness`, `RoadSnow` 및 `RoadLeaf` 효과에는 씬에 [매터리얼](https://github.com/Microsoft/AirSim/tree/master/Unreal/Plugins/AirSim/Content/Weather/WeatherFX)를 추가해야 합니다.

자세한 내용은 [예제 코드](https://github.com/Microsoft/AirSim/blob/master/PythonClient/computer_vision/weather.py)를 참조하십시오.

### Lidar API
AirSim은 차량의 Lidar 센서에서 point cloud 데이터를 검색하는 API를 제공합니다. [settings.json](settings.md)에서 채널 수, 초당 포인트 수, 수평 및 수직 FOV 등의 매개 변수를 설정할 수 있습니다.

[lidar API 및 설정](lidar.md) 및 [센서 설정](sensors.md)에 대한 추가 정보

### 다중 차량
AirSim은 다중 차량을 지원하고 API를 통해 제어합니다. [다중 차량](multi_vehicle.md) 문서를 읽어주세요.

### 좌표계
모든 AirSim API는 NED 좌표계를 사용합니다 (예 : +X는 북쪽, +Y는 동쪽 및 +Z는 아래). 모든 장치는 SI 시스템 상에 있습니다. Unreal Engine이 내부적으로 사용하는 좌표계와는 다릅니다. Unreal Engine에서 +Z는 아래가 아니라 위이며 길이 단위는 미터가 아닌 센티미터입니다. AirSim API는 적절히 변환합니다. 차량의 시작점은 항상 NED 시스템에서 (0, 0, 0) 좌표 입니다. 따라서 Unreal 좌표에서 NED로 변환 할 때 먼저 시작 오프셋을 빼고 cm에서 m으로 변환 할 때 100배로 늘립니다. 차량은 Player Start 컴포넌트가 배치 된 Unreal 환경에서 스폰됩니다. [settings.json](settings.md)에는 `OriginGeopoint`라는 설정이 있으며, 이는 지리적 경도, 경도 및 고도를 Player Start 컴포넌트에 할당합니다.

## 차량 별 API
### 자동차 API
자동차에는 다음과 같은 API가 있습니다:

* `setCarControls`: 스로틀, 스티어링, 핸드 브레이크 및 자동 또는 수동 기어를 설정할 수 있습니다.
* `getCarState`: 속도, 현재 기어 및 위치, 방향, 선형 및 각속도, 선형 및 각가속도 같은 6 가지 운동량을 포함한 상태 정보를 검색합니다. 모든 크기은 NED 좌표계, 차체 프레임에 있는 각속도 및 가속도를 제외한 월드 프레임의 SI 단위입니다.
* [이미지 API](image_apis.md).

### Multirotor API
각도, 속도 벡터, 대상 위치 또는 이들의 조합을 지정하여 multirotor를 제어 할 수 있습니다. 이를 위해 `move*` API가 있습니다. 위치 제어를 할 때 알고리즘을 따르는 경로를 사용해야 합니다. 기본적으로 AirSim은 carrot following 알고리즘을 사용합니다. high level인 목표를 지정하기 만하면 펌웨어가 나머지를 처리하기 때문에 이를 "high level 제어"라고 합니다. AirSim에서 현재 사용 가능한 가장 low level인 제어는 `moveByAngleThrottleAsync` API입니다.

#### getMultirotorState
이 API는 한 번의 호출로 차량 상태를 반환합니다. 상태에는 충돌, 추정 된 운동학(fusing 센서로 계산 된 운동학) 및 타임 스탬프(사건 이후의 나노초)가 포함됩니다. 여기서 운동학은 위치, 방향, 선형 및 각속도, 선형 및 각가속도의 6 가지 크기를 의미합니다. 현재 simple_slight는 상태 추정기를 지원하지 않습니다. 이는 추정 및 지면 상태 운동학 값이 simple_flight와 동일 함을 의미합니다. 그러나 각가속도를 제외하고 PX4에 대해 추정 된 운동학을 사용할 수 있습니다. 모든 크기는 NED 좌표계, 차체 프레임에 있는 각속도 및 가속도를 제외한 월드 프레임의 SI 단위입니다.

#### 비동기 메소드, 기간 및 max_wait_seconds
많은 API 메소드에는 `duration` 또는 `max_wait_seconds`라는 매개 변수가 있으며 접미사로 *Async*가 있습니다 (예 :`takeoffAsync`). 이 메소드는 AirSim에서 작업을 시작한 직후에 반환되므로 해당 작업이 실행되는 동안 클라이언트 코드가 다른 작업을 수행 할 수 있습니다. 이 작업이 완료되기를 기다리는 경우 다음과 같이 `waitOnLastTask`를 호출 할 수 있습니다.

```cpp
//C++
client.takeoffAsync()->waitOnLastTask();
```

```cpp
# Python
client.takeoffAsync().join()
```

다른 명령을 시작하면 이전 작업이 자동으로 취소되고 새 명령이 시작됩니다. 이를 통해 당신의 코드가 지속적으로 감지되는 패턴을 사용하고 추적 할 새로운 궤적을 계산하며 AirSim에서 차량으로 경로를 발행 할 수 있습니다. 새로 발행 된 각 궤적은 이전 궤적을 취소하여 새 센서 데이터가 도착하면 코드에서 지속적으로 업데이트를 수행 할 수 있습니다.

모든 *비동기* 메소드는 Python에서 `concurrent.futures.Future`를 리턴합니다 (C ++에서는 `std::future`). 이 future 클래스는 현재 상태를 확인하거나 작업을 취소 할 수 없습니다. 작업이 완료 될 때까지 기다려야 합니다. 그러나 AirSim은 `cancelLastTask` API를 제공합니다.

#### drivetrain
차량을 비행 할 수있는 두 가지 모드가 있습니다 : `drivetrain` 매개 변수는 `airsim.DrivetrainType.ForwardOnly` 또는 `airsim.DrivetrainType.MaxDegreeOfFreedom`으로 설정되어 있습니다. ForwardOnly를 지정하면, 차량의 전방이 항상 주행 방향을 가리켜야 합니다. 따라서 무인 항공기가 좌회전을 하려면 먼저 전방을 왼쪽으로 돌리십시오. 이 모드는 전면 카메라 만 있고 FPV 뷰를 사용하여 차량을 작동 할 때 유용합니다. 이것은 항상 정면을 바라보는 자동차 여행과 거의 비슷합니다. MaxDegreeOfFreedom은 정면이 가리키는 위치를 신경 쓰지 않는다는 것을 의미합니다. 좌회전하면 게처럼 왼쪽으로 움직이기 시작합니다. Quadrotor는 앞이 가리키는 위치에 관계없이 어느 방향 으로든 갈 수 있습니다. MaxDegreeOfFreedom은이 모드를 활성화합니다.

#### yaw_mode
`yaw_mode`는 `yaw_or_rate` 와 `is_rate`의 두 필드를 가진 `YawMode` 구조체 입니다. `is_rate` 필드가 True인 경우 `yaw_or_rate` 필드는 degrees/sec 단위의 각속도로 해석됩니다. 이는 차량이 이동하는 동안 해당 각속도로 축을 중심으로 연속적으로 회전하기를 원하는 것을 의미합니다. `is_rate`가 False이면 `yaw_or_rate`는 각도 단위로 해석되므로 차량이 특정 각도(즉, yaw)로 회전하고 이동하는 동안 해당 각도를 유지해야 합니다.

`yaw_mode.is_rate == true`인 경우, `drivetrain` 매개 변수가 `ForwardOnly`로 설정되어서는 안된다는 것을 알 수 있습니다. 앞을 계속 가리키고 계속 회전한다는 말로 모순되기 때문입니다. 그러나 `ForwardOnly` 모드에서 `yaw_mode.is_rate = false`를 가지고 있다면 펑키 한 일을 할 수 있습니다. 예를 들어, 원을 그리는 드론과 90도로 설정한 yaw_or_rate가 있으면 카메라는 항상 중앙을 향합니다("super cool selfie mode"). `MaxDegreeofFreedom`에서 `yaw_mode.is_rate = true`를 설정하고 `yaw_mode.yaw_or_rate = 20`이라고 하면 펑키 한 것들을 얻을 수 있습니다. 회전 중에 드론이 경로를 따라 이동하여 360도 스캔 할 수 있습니다.

대부분의 경우, yaw rate를 0으로 설정하여 yaw가 변경되는 것을 원하지 않습니다. 이에 대한 shorthand는 `airsim.YawMode.Zero()`(또는 C++ : `YawMode::Zero()`)입니다.

#### lookahead 및 adaptive_lookahead
차량이 경로를 따르도록 요청하면 AirSim은 "carrot following" 알고리즘을 사용합니다. 이 알고리즘은 경로를 미리보고 속도 벡터를 조정하여 작동합니다. 이 알고리즘의 매개 변수는 `lookahead` 및 `adaptive_lookahead`로 지정됩니다. 대부분의 경우 알고리즘은 단순히 `lookahead = -1` 및 `adaptive_lookahead = 0`으로 설정하여 값을 자동 결정하도록 합니다.

## 실제 차량에서 API 사용
실제 차량에서와 같이 시뮬레이션에서 실행되는 *동일한 코드*를 실행할 수 있기를 원합니다. 이를 통해 시뮬레이터에서 코드를 테스트하고 실제 차량에 배포 할 수 있습니다.

일반적으로 API를 사용하면 실제 차량에서 수행 할 수 없는 작업(예 : 지면 상태 획득)을 수행해서는 안됩니다. 물론 시뮬레이터에는 더 많은 정보가 있으며 실제 차량에서 달리는 데 신경 쓰지 않는 응용 프로그램에 유용합니다. 이러한 이유로 우리는 `sim` 접두사(예 :`simGetGroundTruthKinematics`)를 붙여 sim 전용 API를 명확하게 설명합니다. 이렇게하면 실제 차량에서 코드를 실행하는 데 관심이 있는 경우 이러한 시뮬레이션 전용 API를 사용하지 않아도 됩니다.

AirLib은 Gigabyte barebone Mini PC와 같은 offboard computing module에 넣을 수있는 독립형 라이브러리입니다. 이 모듈은 정확히 동일한 코드와 비행 컨트롤러 프로토콜을 사용하여 PX4와 같은 비행 컨트롤러와 통신 할 수 있습니다. 시뮬레이터에서 테스트하기 위해 작성한 코드는 변경되지 않습니다. [커스텀 드론의 AirLib](custom_drone.md)를 참조하세요.

## AirSim에 새로운 API 추가
새 API를 추가하려면 소스 코드를 수정해야합니다. 대부분의 변경 사항은 기계적이며 AirSim이 지원하는 다양한 수준의 추상화에 필요합니다. [이 커밋](https://github.com/Microsoft/AirSim/commit/f0e83c29e7685e1021185e3c95bfdaffb6cb85dc)은 시뮬레이터 창에 메시지를 출력하는 간단한 API `simPrintLogMessage`를 추가하는 방법을 보여줍니다.

## Some Internals
API는 [TamÃ¡s Szelei](https://github.com/sztomi)가 개발 한 [rpclib](http://rpclib.net/)를 통해 TCP/IP를 통한 [msgpack-rpc 프로토콜](https://github.com/msgpack-rpc/msgpack-rpc)을 사용하므로 C++, C#, Python, Java 등을 포함한 다양한 프로그래밍 언어를 사용할 수 있습니다. AirSim이 시작되면 포트 41451([settings](settings.md)을 통해 변경 가능)을 열고 들어오는 요청을 수신합니다. Python 또는 C++ 클라이언트 코드는 이 포트에 연결하고 [msgpack serialization format](https://msgpack.org)을 사용하여 RPC 호출을 보냅니다.

## 참조 및 예제

* [C++ API 예제](apis_cpp.md)
* [Car 예제](https://github.com/Microsoft/AirSim/tree/master/PythonClient//car)
* [Multirotor 예제](https://github.com/Microsoft/AirSim/tree/master/PythonClient//multirotor)
* [Computer Vision 예제](https://github.com/Microsoft/AirSim/tree/master/PythonClient//computer_vision)
* [경로 상 이동](https://github.com/Microsoft/AirSim/wiki/moveOnPath-demo) Modular Neighborhood 환경을 통한 빠른 Multirotor 비행 데모를 보여주는 비디오
* [Hexacopter 빌드](https://github.com/Microsoft/AirSim/wiki/hexacopter)
* [Point Clouds 빌드](https://github.com/Microsoft/AirSim/wiki/Point-Clouds)


## FAQ

#### API를 실행하면 Unreal 속도가 크게 느려집니다
Unreal Engine 창이 포커스를 잃을 때 Unreal이 급격히 느려지는 것을 본다면, 편집->에디터 개인설정'으로 가서, '검색'창에 'CPU'를 타이핑 하고 'Use Less CPU when in Background'이 체크 해제 되었는지 확인하세요.

#### Windows에서 다른 것이 필요합니까?
VC++, Windows SDK 8.1 및 Python과 함께 VS2017을 설치해야 합니다. Python API를 사용하려면 Python 3.5 이상이 필요합니다(Anaconda를 사용하여 설치하십시오).

#### 어떤 버전의 Python을 사용해야 합니까?
Python 도구 및 라이브러리를 얻으려면 [Anaconda](https://www.anaconda.com/download/)를 권장합니다. 우리의 코드는 Python 3.5.3 :: Anaconda 4.4.0으로 테스트되었습니다. 이 사실은 이전 버전에는 [문제점](https://stackoverflow.com/a/45934992/207661)이있는 것으로 알려져 있으므로 중요합니다.

#### `import cv2`에 오류가 발생합니다
다음을 사용하여 OpenCV를 설치할 수 있습니다:
```
conda install opencv
pip install opencv-python
```

