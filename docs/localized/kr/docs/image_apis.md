# 이미지 API

AirSim API에 익숙하지 않은 경우 [일반 API 문서](apis.md)를 먼저 읽으십시오.

## 단일 이미지 얻기

다음은 "0"이라는 카메라에서 단일 이미지를 가져 오는 샘플 코드입니다. 반환 된 값은 png 형식 이미지의 바이트입니다. 압축되지 않은 형식 및 기타 형식과 사용 가능한 카메라를 보려면 다음 섹션을 참조하십시오.

### Python

```python
import airsim #pip install airsim

# 자동차 용 CarClient()
client = airsim.MultirotorClient()

png_image = client.simGetImage("0", airsim.ImageType.Scene)
# 이미지로 무언가 하기
```

### C++

```cpp
#include "vehicles/multirotor/api/MultirotorRpcLibClient.hpp"

int getOneImage() 
{
    using namespace std;
    using namespace msr::airlib;
    
    //자동차 용 CarRpcLibClient
    msr::airlib::MultirotorRpcLibClient client;

    vector<uint8_t> png_image = client.simGetImage("0", VehicleCameraBase::ImageType::Scene);
    //이미지로 무언가 하기
}
```

## 보다 유연한 이미지 가져오기

`simGetImages` API는 `simGetImage` API보다 사용이 약간 더 복잡합니다. 예를 들어, 단일 API 호출로 왼쪽 카메라에서 왼쪽 카메라 보기, 오른쪽 카메라 보기 및 깊이 이미지를 얻을 수 있습니다.
`simGetImages` API를 사용하면 부동 소수점 단일 채널 이미지(각각 8비트 3채널(RGB) 대신) 뿐만 아니라 압축되지 않은 이미지도 얻을 수 있습니다.

### Python

```python
import airsim #pip install airsim

# 자동차 용 CarClient()
client = airsim.MultirotorClient()

responses = client.simGetImages([
    # png 형식
    airsim.ImageRequest(0, airsim.ImageType.Scene), 
    # 압축되지 않은 RGB 배열 바이트
    airsim.ImageRequest(1, airsim.ImageType.Scene, False, False),
    # 압축되지 않은 부동 소수점 이미지
    airsim.ImageRequest(1, airsim.ImageType.DepthPlanner, True)])
 
 # 이미지 데이터, 포즈, 타임 스탬프 등을 포함하는 응답으로 무언가 하기
```

#### NumPy를 통한 AirSim 이미지 사용

이미지 조작에 numpy를 사용하려는 경우 압축되지 않은 RGB 이미지를 가져온 다음 다음과 같이 numpy로 변환해야합니다.

```python
responses = client.simGetImages([ImageRequest("0", airsim.ImageType.Scene, False, False)])
response = responses[0]

# numpy 배열 얻기
img1d = np.fromstring(response.image_data_uint8, dtype=np.uint8) 

# 배열을 4채널 이미지 배열 H X W X 4 로 변경
img_rgb = img1d.reshape(response.height, response.width, 3)

# 원본 이미지가 수직으로 뒤집힘
img_rgb = np.flipud(img_rgb)

# png로 저장
airsim.write_png(os.path.normpath(filename + '.png'), img_rgb) 
```

#### 빠른 팁
- `simGetImage` API는 `이진 문자열`을 반환하는데, 이 문자열을 이진 파일로 덤핑해 .png 파일을 만들 수 있습니다. 그러나 다른 방법으로 처리하려면, 편리한 함수 `airsim.string_to_uint8_array`를 사용할 수 있습니다. 이것은 이진 문자열을 NumPy uint8 배열로 변환합니다.

- `simGetImages` API는 한 번의 호출로 모든 카메라의 여러 이미지 타입에 대한 요청을 수락 할 수 있습니다. 이미지가 png 압축인지, RGB 비 압축인지 또는 float 배열인지 지정할 수 있습니다. png 압축 이미지의 경우 `이진 문자열`이 나타납니다. float 배열의 경우 float64의 Python 리스트를 얻습니다. 다음을 사용해 이 float 배열을 NumPy 2D 배열로 변환 할 수 있습니다.
    ```
    airsim.list_to_2d_float_array(response.image_data_float, response.width, response.height)
    ```
    `airsim.write_pfm()`함수를 사용하여 float 배열을 .pfm 파일(Portable Float Map 형식)로 저장할 수도 있습니다.

### C++

```cpp
int getStereoAndDepthImages() 
{
    using namespace std;
    using namespace msr::airlib;
    
    typedef VehicleCameraBase::ImageRequest ImageRequest;
    typedef VehicleCameraBase::ImageResponse ImageResponse;
    typedef VehicleCameraBase::ImageType ImageType;

    //자동차 용
    //msr::airlib::CarRpcLibClient client;
    msr::airlib::MultirotorRpcLibClient client;

    //오른쪽, 왼쪽 및 깊이 이미지 얻기. 처음 두 개는 png, 두 번째는 float16
    vector<ImageRequest> request = { 
        //png 형식
        ImageRequest("0", ImageType::Scene),
        //압축되지 않은 RGB 배열 바이트
        ImageRequest("1", ImageType::Scene, false, false),       
        //압축되지 않은 부동 소수점 이미지
        ImageRequest("1", ImageType::DepthPlanner, true) 
    };

    const vector<ImageResponse>& response = client.simGetImages(request);
    //이미지 데이터, 포즈, 타임 스탬프 등을 포함하는 응답으로 무언가 하기
}
```

## 완전히 실행 준비가 된 예제

### Python

샘플 코드를 실행하기 위해 완전히 준비하려면 multirotor에 대한 [AirSimClient 프로젝트의 샘플 코드](https://github.com/Microsoft/AirSim/tree/master/PythonClient//multirotor/hello_drone.py) 또는 [HelloCar 샘플](https://github.com/Microsoft/AirSim/tree/master/PythonClient//car/hello_car.py)을 참조하십시오. 이 코드는 또한 이미지를 파일에 저장하거나 이미지를 조작하기 위해 `numpy`를 사용하는 것과 같은 간단한 활동도 보여줍니다.

### C++

샘플 코드를 실행하기 위해 완전히 준비하려면 multirotor에 대한 [HelloDrone 프로젝트의 샘플 코드](https://github.com/Microsoft/AirSim/tree/master/HelloDrone//main.cpp) 또는 [HelloCar 프로젝트](https://github.com/Microsoft/AirSim/tree/master/HelloCar//main.cpp)를 참조하십시오.

지면 깊이 상태 및 시차(disparity)와 함께 지정된 수의 스테레오 이미지를 생성하고 [pfm 형식](pfm.md)으로 저장하는 [다른 예제 코드](https://github.com/Microsoft/AirSim/tree/master/Examples/DataCollection/StereoImageGenerator.hpp)도 참조하십시오.

## 사용 가능한 카메라
### 자동차
API 호출에서 다음 이름으로 자동차의 카메라에 액세스 할 수 있습니다: `front_center`, `front_right`, `front_left`, `fpv` 그리고 `back_center`. 여기 FPV 카메라는 차에서 운전자의 머리 위치입니다.
### Multirotor
API 호출에서 CV 모드의 카메라는 다음 이름으로 액세스 할 수 있습니다: `front_center`, `front_right`, `front_left`, `bottom_center` 그리고 `back_center`. 
### Computer Vision 모드
카메라 이름은 multirotor와 동일합니다.

### 카메라 이름의 호환성
AirSim v1.2 이전에는 이름 대신 ID 번호를 사용하여 카메라에 액세스했습니다. 이전 버전과의 호환성을 위해 위의 카메라 이름에` "0"`,` "1"`,` "2"`,` "3"`,` "4"`와 같은 ID 번호를 계속 사용할 수 있습니다. 또한 카메라 이름` ""`을 사용하여 일반적으로 카메라 `"0 "`인 기본 카메라에 액세스 할 수도 있습니다.

## "Computer Vision" 모드

AirSim을 소위 "Computer Vision"모드에서 사용할 수 있습니다. 이 모드에서는 물리 엔진이 비활성화되고 차량이 없으며 카메라만 있습니다. 키보드를 사용하여 이동할 수 있습니다(키에 대한 도움말을 보려면 F1 사용하십시오). 녹화 버튼을 눌러 이미지를 계속 생성 할 수 있습니다. 또는 API를 호출하여 카메라를 이동하고 이미지를 찍을 수 있습니다.

이 모드를 활성화하려면 `Documents\AirSim` 폴더(또는 Linux의 경우 `~/Documents/AirSim`)에서 찾을 수 있는 [settings.json](settings.md)을 편집하고 루트 레벨에 다음 값이 있는지 확인하십시오:

```json
{
  "SettingsVersion": 1.2,
  "SimMode": "ComputerVision"
}
```

다음은 카메라를 이동하고 이미지를 캡처하는 [Python 코드 예제](https://github.com/Microsoft/AirSim/tree/master/PythonClient//computer_vision/cv_mode.py)입니다.

이 모드는 [UnrealCV 프로젝트](http://unrealcv.org/)에서 영감을 얻었습니다.

### Computer Vision 모드에서 포즈 설정
API를 사용하여 환경을 이동하려면 `simSetVehiclePose` API를 사용할 수 있습니다. 이 API는 위치와 방향을 취하여 전면 중앙 카메라가 위치한 보이지 않는 차량에서 위치를 설정합니다. 나머지 모든 카메라는 상대 위치를 유지하면서 움직입니다. 위치(또는 방향)를 변경하지 않으려면 위치(또는 방향) 컴포넌트를 부동 소수점 nan 값으로 설정하십시오. `simGetVehiclePose`는 현재 포즈를 검색 할 수 있게 합니다. 또한, `simGetGroundTruthKinematics`를 사용하여 이동에 사용할 운동량을 구할 수 있습니다. 세그먼트 API, 충돌 API 및 카메라 API와 같은, 차량 이외의 많은 API도 사용할 수 있습니다.

## 카메라 API
`simGetCameraInfo`는 지정된 카메라의 포즈(월드 프레임, NED 좌표, SI 단위에서) 및 FOV(도 단위)를 반환합니다. [사용 예제](https://github.com/Microsoft/AirSim/tree/master/PythonClient//computer_vision/cv_mode.py)를 참조하십시오.

`simSetCameraOrientation`은 NED 프레임에서 지정된 카메라의 방향을 쿼터니언으로 설정합니다. 편리한 `airsim.to_quaternion()` 함수를 사용하면 pitch, roll, yaw를 쿼터니언으로 변환 할 수 있습니다. 예를 들어 camera-0을 15도 pitch로 설정하려면 다음을 사용할 수 있습니다:
```
client.simSetCameraOrientation(0, airsim.to_quaternion(0.261799, 0, 0)); #radians
```

### 짐벌
[설정](settings.md#gimbal)을 사용하여 모든 카메라의 pitch, roll 또는 yaw에 대한 안정화를 설정할 수 있습니다

[사용 예제](https://github.com/Microsoft/AirSim/tree/master/PythonClient//computer_vision/cv_mode.py)를 참조하십시오.

## 해상도 및 카메라 매개 변수 변경
해상도, FOV 등을 변경하려면 [settings.json](settings.md)을 사용하십시오. 예를 들어 아래의 settings.json의 추가 항목은 장면 캡처에 대한 매개 변수를 설정하고 위에서 설명한 "Computer Vision" 모드를 사용합니다. 설정을 생략하면 아래의 기본값이 사용됩니다. 자세한 내용은 [설정 문서](settings.md)를 참조하십시오. 스테레오 카메라를 사용하는 경우 현재 왼쪽과 오른쪽 사이의 거리는 25cm로 고정되어 있습니다.

```json
{
  "SettingsVersion": 1.2,
  "CameraDefaults": {
      "CaptureSettings": [
        {
          "ImageType": 0,
          "Width": 256,
          "Height": 144,
          "FOV_Degrees": 90,
          "AutoExposureSpeed": 100,
          "MotionBlurAmount": 0
        }
    ]
  },
  "SimMode": "ComputerVision"
}
```

## 다른 이미지 타입에서 픽셀 값의 의미는 무엇입니까?
### 사용 가능한 이미지 타입 값
```cpp
  Scene = 0, 
  DepthPlanner = 1, 
  DepthPerspective = 2,
  DepthVis = 3, 
  DisparityNormalized = 4,
  Segmentation = 5,
  SurfaceNormals = 6,
  Infrared = 7
```                

### DepthPlanner 와 DepthPerspective
일반적으로 깊이 이미지를 float (예 :`pixels_as_float = true`로 설정)로 검색하고 `ImageRequest`에서 `ImageType = DepthPlanner` 또는 `ImageType = DepthPerspective`를 지정하려고 합니다. `ImageType = DepthPlanner`의 경우 카메라 평면도에서 깊이를 얻습니다. 즉, 카메라와 평행 한 평면에 있는 모든 점의 깊이가 동일합니다. `ImageType = DepthPerspective`의 경우 해당 픽셀에 닿는 프로젝션 광선을 사용하여 카메라에서 깊이를 얻습니다. 사용 사례에 따라 planner 깊이 또는 원근 깊이가 원하는 지면 상태 이미지 일 수 있습니다. 예를 들어 Point Cloud를 생성하기 위해 `depth_image_proc`와 같은 ROS 패키지에 원근 깊이를 제공 할 수 있습니다. 또는 planner 깊이는 SGM과 같은 스테레오 알고리즘에 의해 생성 된 추정 깊이 이미지를 제공할 수 있습니다.

### DepthVis
`ImageRequest`에서`ImageType = DepthVis`를 지정하면 깊이 시각화에 도움이 되는 이미지가 나타납니다. 이 경우 각 픽셀 값은 카메라 평면의 깊이(미터)에 따라 검은 색에서 흰색으로 보간됩니다. 순수한 흰색의 픽셀은 100m 이상의 깊이를 의미하고 순수한 검정색은 0m의 깊이를 의미합니다.

### DisparityNormalized
일반적으로 시차(disparity) 이미지를 float로 검색(즉,`pixels_as_float = true`로 설정하고 `ImageRequest`에서 `ImageType = DisparityNormalized`를 지정)하려는 경우, 각 픽셀은 0에서 1 사이의 값인 `(Xl-Xr) / Xmax`입니다.

### Segmentation
`ImageRequest`에서 `ImageType = Segmentation`을 지정하면, 씬의 지면 상태 세그먼트을 제공하는 이미지를 얻습니다. 시작시 AirSim은 환경에서 사용 가능한 각 메시에 0에서 255 사이의 값을 할당합니다. 이 값은 [팔레트](https://github.com/Microsoft/AirSim/tree/master/Unreal//Plugins/AirSim/Content/HUDAssets/seg_color_pallet.png)의 특정 색상에 매핑되지 않습니다. 각 객체 ID의 RGB 값은 [이 파일](seg_rgbs.txt)에서 찾을 수 있습니다.

API를 사용하여 특정 값(0-255 범위로 제한됨)을 특정 메시에 할당 할 수 있습니다. 예를 들어, 아래의 Python 코드는 Blocks 환경에서 "Ground"라는 메시의 오브젝트 ID를 20으로 설정하므로 세그먼트 뷰에서 색상이 변경됩니다.

```python
success = client.simSetSegmentationObjectID("Ground", 20);
```

메시가 발견되었는지 알려주는 반환 값은 bool 타입입니다.

Blocks와 같은 일반적인 Unreal 환경에는 일반적으로 "Ground_2", "Ground_3" 등과 같은 동일한 오브젝트로 구성된 다른 많은 메시가 있습니다. 이러한 모든 메시에 대해 객체 ID를 설정하는 것이 지루하므로 AirSim은 정규식도 지원합니다. 예를 들어, 아래 코드는 한 줄만으로 이름이 "ground"(대소 문자 무시)로 시작하는 모든 메시를 21로 설정합니다.

```python
success = client.simSetSegmentationObjectID("ground[\w]*", 21, True);
```

정규식 일치를 사용하여 하나 이상의 메쉬를 찾은 경우 반환 값은 true입니다.

세그먼트 이미지에 대한 정확한 RGB 값을 얻으려면 이 API를 사용하여 압축되지 않은 이미지를 요청하는 것이 좋습니다:
```python
responses = client.simGetImages([ImageRequest(0, AirSimImageType.Segmentation, False, False)])
img1d = np.fromstring(response.image_data_uint8, dtype=np.uint8) #numpy 배열 얻기
img_rgb = img1d.reshape(response.height, response.width, 3) # 배열을 3채널 이미지 배열 H X W X 3 으로 변경
img_rgb = np.flipud(img_rgb) #원본 이미지가 수직으로 뒤집힘

#독특한 색 찾기
print(np.unique(img_rgb[:,:,0], return_counts=True)) #red
print(np.unique(img_rgb[:,:,1], return_counts=True)) #green
print(np.unique(img_rgb[:,:,2], return_counts=True)) #blue  
```

완전히 실행 준비가 된 예제는 [segmentation.py](https://github.com/Microsoft/AirSim/tree/master/PythonClient//computer_vision/segmentation.py)에서 확인할 수 있습니다.

#### 객체 ID 설정 해제
세그먼트 이미지에 표시되지 않도록 객체의 ID를 -1로 설정할 수 있습니다.

#### 메쉬 이름을 찾는 방법
원하는 지면 상태 세그먼트을 얻으려면 Unreal 환경에서 메시의 이름을 알아야합니다. 이렇게 하려면 Unreal 에디터에서 Unreal 환경을 열고 월드 아웃 라이너를 사용하여 관심있는 메시의 이름을 검사해야 합니다. 예를 들어 아래는 에디터의 Blocks 환경에서 오른쪽 패널에 있는 Ground에 대한 메시 이름을 나타냅니다.

![record screenshot](images/unreal_editor_blocks.png)

Unreal 에디터에서 Unreal 환경을 여는 방법을 모르는 경우 [소스에서 빌드](build_windows.md) 가이드를 따르십시오.

관심있는 메시를 결정한 후에는 해당 이름을 기록하고 위의 API를 사용하여 객체 ID를 설정하십시오. 객체 ID 생성 동작을 변경하는 데 사용할 수있는 [몇 가지 설정](settings.md#segmentation-settings)이 있습니다.

#### 객체 ID의 색상 변경
현재 각 개체 ID의 색상은 [이 팔레트](https://github.com/Microsoft/AirSim/tree/master/Unreal//Plugins/AirSim/Content/HUDAssets/seg_color_pallet.png)와 같이 고정되어 있습니다. 곧 객체 ID의 색상을 원하는 값으로 변경하는 기능을 추가 할 예정입니다. 그 동안 즐겨 사용하는 이미지 편집기에서 세그먼트 이미지를 열고 관심있는 RGB 값을 얻을 수 있습니다.

#### 시작 객체 ID
처음에 AirSim은 `UStaticMeshComponent` 또는 `ALandscapeProxy` 타입의 환경에서 발견 된 각 객체에 객체 ID를 할당합니다. 그런 다음 메쉬 이름 또는 소유자 이름 (설정에 따라 다름)을 사용하고 소문자로 ASCII 97 미만의 문자를 제거하여 숫자와 일부 문장 부호를 제거하고 모든 문자의 int 값과 모듈로(modulo) 255를 합하여 오브젝트 ID를 생성합니다. 다시 말해, 알파벳 문자가 동일한 모든 객체는 동일한 객체 ID를 갖습니다. 이 휴리스틱은 많은 Unreal 환경에서 간단하고 효과적이지만 원하는 것이 아닐 수도 있습니다. 이 경우 위의 API를 사용하여 객체 ID를 원하는 값으로 변경하십시오. 이 동작을 변경하는 데 사용할 수 있는 [몇 가지 설정](settings.md#segmentation-settings)이 있습니다.

#### 메시의 오브젝트 ID 얻기
`simGetSegmentationObjectID` API를 사용하면 주어진 메시 이름에 대한 객체 ID를 얻을 수 있습니다.

### 적외선
현재 이것은 그레이 스케일 0-255까지의 오브젝트 ID에서의 맵입니다. 따라서 오브젝트 ID 42의 메시는 색상 (42, 42, 42)으로 표시됩니다. 객체 ID 설정 방법에 대한 자세한 내용은 [세그먼트 섹션](#segmentation)을 참조하십시오. 일반적으로 이 이미지 유형에 노이즈 설정을 적용하면 좀 더 사실적인 효과를 얻을 수 있습니다. 우리는 여전히 다른 적외선 아티팩트를 추가하기 위해 노력하고 있으며 어떤 기여든지 환영합니다.

## 예제 코드
임의의 위치와 방향에서 차량 위치를 설정한 다음, 이미지를 촬영하는 완전한 예제는 [GenerateImageGenerator.hpp](https://github.com/Microsoft/AirSim/tree/master/Examples/DataCollection/StereoImageGenerator.hpp)에서 확인할 수 있습니다. 이 예에서는 지정된 수의 스테레오 이미지와 실제 시차(disparity) 이미지를 생성하여 [pfm 포멧](pfm.md)으로 저장합니다.
