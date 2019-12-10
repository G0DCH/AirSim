# AirSim에서 다중 차량

1.2 버전부터 AirSim은 다중 차량을 완벽하게 지원합니다. 이 기능을 통해 여러 차량을 쉽게 생성하고 API를 사용하여 차량을 제어 할 수 있습니다.

## 다중 차량 생성
[settings.json](settings.md)에서 쉽게 지정할 수 있습니다. `차량` 요소를 사용하면 만들려는 차량 목록과 초기 위치 및 방향을 지정할 수 있습니다. 위치는 Unreal 환경의 플레이어 시작 컴포넌트에서 원점이 설정된 SI 단위로 NED 좌표로 지정됩니다. 방향은 각도에서 Yaw, Pitch 및 Roll로 지정됩니다.

### 다중 자동차 생성
```json
{
	"SettingsVersion": 1.2,
	"SimMode": "Car",
	
	"Vehicles": {
		"Car1": {
		  "VehicleType": "PhysXCar",
		  "X": 4, "Y": 0, "Z": -2
		},
		"Car2": {
		  "VehicleType": "PhysXCar",
		  "X": -4, "Y": 0, "Z": -2,
      "Yaw": 90
		}
  }
}
```

### 다중 드론 생성
```json
{
	"SettingsVersion": 1.2,
	"SimMode": "Multirotor",
	
	"Vehicles": {
		"Drone1": {
		  "VehicleType": "SimpleFlight",
		  "X": 4, "Y": 0, "Z": -2,
      "Yaw": -180
		},
		"Drone2": {
		  "VehicleType": "SimpleFlight",
		  "X": 8, "Y": 0, "Z": -2
		}

    }
}
```

## 다중 차량에 API 사용
AirSim 1.2 이후의 새로운 API를 사용하면 `vehicle_name`을 지정할 수 있습니다. 이 이름은 json 설정의 키 값(예 : Car1 또는 Drone2)에 해당합니다.

[자동차 예제 코드](https://github.com/Microsoft/AirSim/tree/master/PythonClient//car/multi_agent_car.py)

[멀티로터 예제 코드](https://github.com/Microsoft/AirSim/tree/master/PythonClient//multirotor/multi_agent_drone.py)

### 데모
[![AirSimMultiple Vehicles Demo Video](images/demo_multi_vehicles.png)](https://youtu.be/35dgcuLuF5M)
