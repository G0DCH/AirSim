# API 클라이언트 코드 업그레이드
AirSim v1.2에는 몇 가지 API 변경 사항이 있어 불일치를 제거하고 향후 확장성을 추가하며 보다 깔끔한 인터페이스를 제공하고자 합니다. 그러나 이러한 변경 사항 중 많은 부분이 *중단 변경 사항*이므로 AirSim과 통신하는 클라이언트 코드를 수정해야 합니다.

## 빠른 방법
클라이언트 코드에서 수행해야 할 대부분의 변경 사항은 매우 쉽지만, 더 빠른 방법은 [Hello Drone](https://github.com/Microsoft/AirSim/tree/master/PythonClient//multirotor/hello_drone.py) 또는 [Hello Car](https://github.com/Microsoft/AirSim/tree/master/PythonClient//car/hello_car.py)와 같은 예제 코드를 살펴보면 변경 내용을 쉽게 파악할 수 있습니다.

## AirSim 가져 오기
이것 대신

```python
from AirSimClient import *
```
이것을 사용하십시오:

```python
import airsim
```

위는 다음을 사용하여 AirSim 모듈을 설치했다고 가정합니다.
```
pip install --user airsim
```

리포지토리의 PythonClient 폴더에서 코드를 실행하는 경우 다음을 수행 할 수도 있습니다.

```python
import setup_path 
import airsim
```

여기에 setup_path.py가 폴더에 있어야하며 `PythonClient` 리포지토리 폴더에 `airsim` 패키지의 경로를 설정합니다. PythonClient 폴더의 모든 예제는 이 방법을 사용합니다.

## AirSim Class 사용
이제 패키지에 모든 것이 포함되어 있으므로 아래와 같이 AirSim 클래스에 대해 명시적인 네임 스페이스를 사용해야 합니다.

이것 대신

```python
client1 = CarClient()
```

이것을 사용하십시오:

```python
client1 = airsim.CarClient()
```

## AirSim 타입

우리는 모든 타입을 `airsim` 네임 스페이스로 옮겼습니다.

이것 대신

```python
image_type = AirSimImageType.DepthVis

d = DrivetrainType.MaxDegreeOfFreedom
```

이것을 사용하십시오:

```python
image_type = airsim.ImageType.DepthVis

d = airsim.DrivetrainType.MaxDegreeOfFreedom
```

## 이미지 얻기

아래에 새로운 것은 없으며 위의 조합일 뿐입니다. 이전에 `camera_id`를 사용했던 모든 API는 이제 `camera_name`을 대신 사용합니다. 여기에서 [사용 가능한 카메라](image_apis.md#avilable_cameras)를 볼 수 있습니다.

이것 대신

```python
responses = client.simGetImages([ImageRequest(0, AirSimImageType.DepthVis)])
```

이것을 사용하십시오:

```python
responses = client.simGetImages([airsim.ImageRequest("0", airsim.ImageType.DepthVis)])
```

## 유틸리티 메소드
이전 버전에서는 AirSimClientBase의 일부로 몇 가지 유틸리티 메소드를 제공했습니다. 이 방법들은 더 많은 파이썬 인터페이스를 위해 `airsim` 네임 스페이스로 옮겨졌습니다.

이것 대신

```python
AirSimClientBase.write_png(my_path, img_rgba) 

AirSimClientBase.wait_key('Press any key')
```

이것을 사용하십시오:

```python
airsim.write_png(my_path, img_rgba)

airsim.wait_key('Press any key')
```

## Camera Names
AirSim은 이제 인덱스 번호 대신 [이름](image_apis.md # available_cameras)을 사용하여 카메라를 참조합니다. 그러나 이전 버전과의 호환성을 유지하기 위해 이러한 이름은 이전 색인 번호를 문자열로 사용하여 별명을 지정합니다.

이것 대신

```python
client.simGetCameraInfo(0)
```

이것을 사용하십시오:

```python
client.simGetCameraInfo("0")

# 또는

client.simGetCameraInfo("front-center")
```

## 비동기 메소드
멀티 로터의 경우 AirSim에는 `takeoff` 또는 `moveByVelocityZ`와 같이 완료하는 데 시간이 오래 걸리는 다양한 방법이 있습니다. 이러한 모든 방법의 이름은 이제 아래 표시된 것처럼 접미사 *Async*를 추가하여 이름이 변경됩니다.

이것 대신

```python
client.takeoff()

client.moveToPosition(-10, 10, -10, 5)
```

이것을 사용하십시오:

```python
client.takeoffAsync().join()

client.moveToPositionAsync(-10, 10, -10, 5).join()
```

`.join ()`은 비동기 호출이 완료되기를 기다리는 Python의 `Future`클래스에 대한 호출입니다. 호출이 진행되는 동안 다른 계산을 수행하도록 선택할 수도 있습니다.

## Simulation-Only Methods
이제 실제 차량에서 사용할 수 있는 메소드와 시뮬레이션에서만 사용할 수 있는 메소드가 명확하게 구분됩니다. 시뮬레이션 전용 방법은 아래에 표시된 것처럼 `sim`이 접두사로 사용됩니다.

```
getCollisionInfo()      is renamed to       simGetCollisionInfo()
getCameraInfo()         is renamed to       simGetCameraInfo()
setCameraOrientation()  is renamed to       simSetCameraOrientation()
```

## 상태 정보
이전의 `CarState`는 `kinematics_true`와 같은 시뮬레이션 전용 정보를 혼합했습니다. 앞으로 CarState에는 실제 세계에서 얻을 수 있는 정보만 포함됩니다.

```python
k = car_state.kinematics_true
```

이것을 사용하십시오:

```python
k = car_state.kinematics_estimated

# 또는

k = client.simGetGroundTruthKinematics()
```