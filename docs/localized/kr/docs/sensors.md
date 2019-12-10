# AirSim의 센서

AirSim은 현재 다음과 같은 센서를 지원합니다.
각 센서는 센서 유형을 지정하는 정수 열거 형과 연관됩니다.

* Camera
* Barometer = 1
* Imu = 2
* Gps = 3
* Magnetometer = 4
* Distance Sensor = 5 
* Lidar = 6

**참고** : 카메라는 다른 센서와 다르게 구성되며 열거 형이 없습니다. 카메라 구성 및 API는 [일반 설정](settings.md) 및 [이미지 API](image_apis.md)를 참조하십시오.

## 기본 센서

`settings.json`에 센서가 지정되지 않은 경우, 다음 센서는 sim 모드에 따라 기본적으로 활성화됩니다.

### 멀티로터
* Imu
* Magnetometer
* Gps
* Barometer

### 자동차
* Gps

### ComputerVision
* None

씬 뒤에는 `settings.json`파일에 지정된 sim 모드에 따라 위의 센서를 기본 매개 변수로 설정하는 [AirSimSettings.hpp](https://github.com/Microsoft/AirSim/blob/master/AirLib/include/common/AirSimSettings.hpp)의 'createDefaultSensorSettings' 메소드가 있습니다.

## 기본 센서 리스트 구성

기본 센서 리스트는 `settings.json`에서 구성 할 수 있습니다:

```JSON
"DefaultSensors": {
    "Barometer": {
         "SensorType": 1,
         "Enabled" : true
    },
    "Imu": {
         "SensorType": 2,
         "Enabled" : true
    },
    "Gps": {
         "SensorType": 3,
         "Enabled" : true
    },
    "Magnetometer": {
         "SensorType": 4,
         "Enabled" : true
    },
    "Distance": {
         "SensorType": 5,
         "Enabled" : true
    },
    "Lidar2": { 
         "SensorType": 6,
         "Enabled" : true,
         "NumberOfChannels": 4,
         "PointsPerSecond": 10000
    }
},
```

## 차량 별 센서 리스트 구성

차량이 센서 목록을 제공하는 경우 **전체 목록을 제공해야합니다**. 기본 센서 목록의 선택적 추가/제거/업데이트는 **지원되지 않습니다**.
차량 별 센서 목록은 json 파일의 차량 설정 부분에서 지정할 수 있습니다.
e.g.,

```
    "Vehicles": {

        "Drone1": {
            "VehicleType": "SimpleFlight",
            "AutoCreate": true,
            ...
            "Sensors": {
                "MyLidar1": { 
                    "SensorType": 6,
                    "Enabled" : true,
                    "NumberOfChannels": 16,
                    "PointsPerSecond": 10000,
                    "X": 0, "Y": 0, "Z": -1,
                    "DrawDebugPoints": true
                },
                "MyLidar2": { 
                    "SensorType": 6,
                    "Enabled" : true,
                    "NumberOfChannels": 4,
                    "PointsPerSecond": 10000,
                    "X": 0, "Y": 0, "Z": -1,
                    "DrawDebugPoints": true
                }
            }
        }
   }
```

### 센서 별 설정
각 센서 유형에는 고유한 설정 세트가 있습니다.
Lidar 특정 설정의 예는 [lidar](lidar.md)를 참조하십시오.

## 센서 API
예제 사용을 위해 [`hello_drone.py`](https://github.com/Microsoft/AirSim/blob/master/PythonClient/multirotor/hello_drone.py) 또는 [`hello_drone.cpp`](https://github.com/Microsoft/AirSim/blob/master/HelloDrone/main.cpp)로 바로 이동하거나, 전체 API는 다음을 참조하십시오.

- Barometer

    C++
    ```cpp
    msr::airlib::BarometerBase::Output getBarometerData(const std::string& barometer_name, const std::string& vehicle_name);
    ```

    Python
    ```python
    barometer_data = getBarometerData(barometer_name = "", vehicle_name = "")
    ```

- IMU

C++
    ```cpp
    msr::airlib::ImuBase::Output getImuData(const std::string& imu_name = "", const std::string& vehicle_name = "");
    ```

    Python
    ```python
    imu_data = getImuData(imu_name = "", vehicle_name = "")
    ```

- GPS

    C++
    ```cpp
    msr::airlib::GpsBase::Output getGpsData(const std::string& gps_name = "", const std::string& vehicle_name = "");
    ```
    Python
    ```python
    gps_data = getGpsData(gps_name = "", vehicle_name = "")
    ```

- Magnetometer

    C++
    ```cpp
    msr::airlib::MagnetometerBase::Output getMagnetometerData(const std::string& magnetometer_name = "", const std::string& vehicle_name = "");
    ```
    Python
    ```python
    magnetometer_data = getMagnetometerData(magnetometer_name = "", vehicle_name = "")
    ```

- Distance sensor

    C++
    ```cpp
    msr::airlib::DistanceBase::Output getDistanceSensorData(const std::string& distance_sensor_name = "", const std::string& vehicle_name = "");
    ```
    Python
    ```python
    distance_sensor_name = getDistanceSensorData(distance_sensor_name = "", vehicle_name = "")
    ```

- Lidar   
    See [lidar](lidar.md) for Lidar API.
