# AirSim에서 Lidar를 사용하는 방법

AirSim은 멀티 로터 및 차량용 Lidar를 지원합니다.

Lidar와 다른 Lidar 설정을 활성화하려면 `AirSimSettings.json`을 통해 구성 할 수 있습니다.
일반/공유 센서 설정 구성에 대한 정보는 [일반 센서](sensors.md)를 참조하십시오.

## 차량에서 Lidar를 활성화
* 기본적으로 lidar는 활성화되어 있지 않습니다. Lidar를 활성화하려면 `Settings.json`에서 SensorType 및 Enabled 속성을 설정하십시오.
```
        "Lidar1": { 
             "SensorType": 6,
             "Enabled" : true,
```
* 차량에서 여러 대의 Lidar를 사용할 수 있습니다.

## Lidar 구성
`Settings.json`을 통해 다음 매개 변수를 지금 구성 할 수 있습니다.

Parameter                 | Description
--------------------------| ------------
NumberOfChannels          | Lidar의 채널/레이저 수
Range                     | 범위(미터 단위)
PointsPerSecond           | 초당 캡처 된 포인트 수
RotationsPerSecond        | 초당 회전
HorizontalFOVStart        | Lidar의 수평 FOV 시작(도 단위)
HorizontalFOVEnd          | Lidar의 수평 FOV 끝(도 단위)
VerticalFOVUpper          | Lidar의 수직 FOV 상한(도 단위)
VerticalFOVLower          | Lidar의 수직 FOV 하한(도 단위)
X Y Z                     | 차량에 대한 Lidar의 위치 (NED, 미터 단위)
Roll Pitch Yaw            | 차량에 대한 Lidar의 방향 (도 단위, 전방 벡터 +X에 대한 yaw-pitch-roll 순서)
DataFrame                 | 출력 포인트의 프레임("VehicleInertialFrame" 또는 "SensorLocalFrame")

e.g.,
```
{
    "SeeDocsAt": "https://github.com/Microsoft/AirSim/blob/master/docs/settings_json.md",
    "SettingsVersion": 1.2,

    "SimMode": "Multirotor",

     "Vehicles": {
		"Drone1": {
			"VehicleType": "simpleflight",
			"AutoCreate": true,
			"Sensors": {
			    "LidarSensor1": { 
					"SensorType": 6,
					"Enabled" : true,
					"NumberOfChannels": 16,
					"RotationsPerSecond": 10,
					"PointsPerSecond": 100000,
					"X": 0, "Y": 0, "Z": -1,
					"Roll": 0, "Pitch": 0, "Yaw" : 0,
					"VerticalFOVUpper": -15,
					"VerticalFOVLower": -25,
					"HorizontalFOVStart": -20,
					"HorizontalFOVEnd": 20,
					"DrawDebugPoints": true,
					"DataFrame": "SensorLocalFrame"
				},
				"LidarSensor2": { 
				   "SensorType": 6,
					"Enabled" : true,
					"NumberOfChannels": 4,
					"RotationsPerSecond": 10,
					"PointsPerSecond": 10000,
					"X": 0, "Y": 0, "Z": -1,
					"Roll": 0, "Pitch": 0, "Yaw" : 0,
					"VerticalFOVUpper": -15,
					"VerticalFOVLower": -25,
					"DrawDebugPoints": true,
					"DataFrame": "SensorLocalFrame"
				}
			}
		}
    }
}
```

## 디버깅을 위한 서버 측 시각화
기본적으로 lidar 포인트는 뷰 포트에 그려지지 않습니다. 뷰 포트에서 히트 레이저 포인트를 그리려면 `settings.json`을 통해 'DrawDebugPoints'설정을 활성화하십시오.
e.g.,
```
        "Lidar1": { 
             ...
             "DrawDebugPoints": true
        },
```

## 클라이언트 API
`getdardarData()` API를 사용하여 Lidar 데이터를 검색하십시오.
* 이 API는 캡처 및 Lidar 포즈의 타임 스탬프와 함께 부동 소수점 배열로 Point-Cloud를 반환합니다.
* Point-Cloud: 
  * float 배열은 마지막 스캔 범위 내에서 히트 한 각 포인트의 [x, y, z] 좌표를 나타냅니다.
  * "DataFrame" 속성을 사용하여 출력 포인트의 프레임을 구성 할 수 있습니다.
  "" 또는 "VehicleInertialFrame" -- 기본값; return 된 포인트는 차량 관성 프레임(NED, 미터 단위)입니다.
  "SensorLocalFrame" -- return 된 포인트는 lidar 로컬 프레임에 있습니다 (NED, 미터 단위).
* Lidar Pose:
    * 차량 관성 프레임의 Lidar 포즈(NED, 미터 단위)
    * 포인트를 다른 프레임으로 변환하는 데 사용할 수 있습니다.

### Python 예제
[drone_lidar.py](https://github.com/Microsoft/AirSim/tree/master/PythonClient//multirotor)
[car_lidar.py](https://github.com/Microsoft/AirSim/tree/master/PythonClient//car)

## Coming soon
* 클라이언트 측의 Lidar 데이터 시각화.