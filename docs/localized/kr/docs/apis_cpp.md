# AirSim용 C++ API 사용

아직 [일반 API 문서](apis.md)를 읽지 않았다면 먼저 읽으십시오. 이 문서는 C++ 예제 및 기타 C++ 특정 세부 사항을 설명합니다.

## 빠른 시작
가장 빠른 시작 방법은 Visual Studio 2017에서 AirSim.sln을 여는 것입니다. 솔루션에 [Hello Car](https://github.com/Microsoft/AirSim/tree/master/HelloCar/) 및 [Hello Drone](https://github.com/Microsoft/AirSim/tree/master/HelloDrone/) 예제가 표시됩니다. 다음 예제에서는 VC++ 프로젝트에서 설정해야 할 include 경로 및 lib 경로를 보여 줍니다. 
Linux를 사용하는 경우 [cmake 파일](https://github.com/Microsoft/AirSim/tree/master/cmake//HelloCar/CMakeLists.txt) 또는 컴파일러 커맨드 라인에서 이러한 경로를 지정합니다.

#### Include 및 Lib 폴더
* Include 폴더: `$(ProjectDir)..\AirLib\deps\rpclib\include;include;$(ProjectDir)..\AirLib\deps\eigen3;$(ProjectDir)..\AirLib\include`
* Dependencies: `rpc.lib`
* Lib 폴더: `$(ProjectDir)\..\AirLib\deps\MavLinkCom\lib\$(Platform)\$(Configuration);$(ProjectDir)\..\AirLib\deps\rpclib\lib\$(Platform)\$(Configuration);$(ProjectDir)\..\AirLib\lib\$(Platform)\$(Configuration)`

## Hello Car

C++을 사용하여 AirSim API를 사용하여 시뮬레이션 자동차를 제어하는 ​​방법은 다음과 같습니다 ([Python 예제](apis.md#hello_car) 참조):

```cpp

// 실행 준비가 된 예제: https://github.com/Microsoft/AirSim/blob/master/HelloCar/main.cpp

#include <iostream>
#include "vehicles/car/api/CarRpcLibClient.hpp"

int main() 
{
    msr::airlib::CarRpcLibClient client;
    client.enableApiControl(true); //이것은 수동 제어를 비활성화합니다
    CarControllerBase::CarControls controls;

    std::cout << "Press enter to drive forward" << std::endl; std::cin.get();
    controls.throttle = 1;
    client.setCarControls(controls);

    std::cout << "Press Enter to activate handbrake" << std::endl; std::cin.get();
    controls.handbrake = true;
    client.setCarControls(controls);

    std::cout << "Press Enter to take turn and drive backward" << std::endl; std::cin.get();
    controls.handbrake = false;
    controls.throttle = -1;
    controls.steering = 1;
    client.setCarControls(controls);

    std::cout << "Press Enter to stop" << std::endl; std::cin.get();
    client.setCarControls(CarControllerBase::CarControls());

    return 0;
}
```

## Hello Drone

C++을 사용하여 AirSim API를 사용하여 시뮬레이션 quadrotor를 제어하는 ​​방법은 다음과 같습니다 ([Python 예제](apis.md#hello_drone) 참조):

```cpp

// 실행 준비가 된 예제: https://github.com/Microsoft/AirSim/blob/master/HelloDrone/main.cpp

#include <iostream>
#include "vehicles/multirotor/api/MultirotorRpcLibClient.hpp"

int main() 
{
    using namespace std;
    msr::airlib::MultirotorRpcLibClient client;

    cout << "Press Enter to enable API control" << endl; cin.get();
    client.enableApiControl(true);

    cout << "Press Enter to arm the drone" << endl; cin.get();
    client.armDisarm(true);

    cout << "Press Enter to takeoff" << endl; cin.get();
    client.takeoffAsync(5)->waitOnLastTask();

    cout << "Press Enter to move 5 meters in x direction with 1 m/s velocity" << endl; cin.get();  
    auto position = client.getMultirotorState().getPosition(); // from current location
    client.moveToPositionAsync(position.x() + 5, position.y(), position.z(), 1)->waitOnLastTask();

    cout << "Press Enter to land" << endl; cin.get();
    client.landAsync()->waitOnLastTask();

    return 0;
}
```

## 참조
* 다른 프로젝트에서 AirSim의 내부 인프라를 사용하는 방법의 [예](../Examples)
* [DroneShell](../DroneShell) 앱은 C++ API를 사용하여 드론을 제어하는 ​​간단한 인터페이스를 만드는 방법을 보여줍니다.
* [Python API](apis.md)

