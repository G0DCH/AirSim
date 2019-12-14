
# AirSim에서 Blocks 환경 설정

Blocks 환경은 리포지토리에서 `Unreal/Environments/Blocks` 폴더로 제공되며 크기가 가볍게 디자인되었습니다. 그것은 매우 기본적이지만 빠르다는 것을 의미합니다.

다음은 Blocks 환경을 시작하고 실행하는 빠른 단계입니다.

## Windows

1. [Unreal을 설치하고 AirSim을 설치했는지](build_windows.md) 확인하십시오.
2. `AirSim\Unreal\Environments\Blocks` 폴더로 이동하여 `update_from_git.bat`를 실행하십시오.
3. 생성 된 .sln 파일을 더블 클릭하여 Visual Studio 2017버전 이상에서 엽니다.
4. `Blocks` 프로젝트가 시작 프로젝트인지, 빌드 구성은 `DebugGame_Editor` 및 `Win64`로 설정되어 있는지 확인하십시오. F5를 눌러 실행하세요.
5. Unreal 에디터에서 Play 버튼을 누르면 아래와 같은 비디오가 나타납니다. [AirSim 사용법](https://github.com/Microsoft/AirSim/#how-to-use-it)을 참조하십시오.

### 코드 변경 및 다시 빌드 하기
Windows의 경우 Visual Studio에서 코드를 변경하고 F5를 누른 후 다시 실행할 수 있습니다. `AirSim\Unreal\Environments\Blocks` 폴더에는 사용 가능한 배치 파일이 거의 없으므로 코드를 동기화하고 정리할 수 있습니다.

## Linux
1. [Unreal 엔진과 AirSim을 빌드했는지](build_linux.md) 확인하십시오.
2. UnrealEngine 저장 폴더로 이동하여 `Engine/Binaries/Linux/UE4Editor`를 실행하면 Unreal Editor가 시작됩니다.
3. 처음 시작할 때 UE4 에디터에 프로젝트가 표시되지 않을 수 있습니다. 프로젝트 탭을 클릭하고 찾아보기 버튼을 클릭 한 다음 `AirSim/Unreal/Environments/Blocks/Blocks.uproject`로 이동하십시오.
4. 호환되지 않는 버전 및 변환에 대한 프롬프트가 표시되면 일반적으로 "추가" 옵션 아래에 있는 전체 변환을 선택하십시오. 누락 된 모듈에 대한 프롬프트가 표시되면 아니오를 선택하여 종료하지 않도록 하십시오.
5. 마지막으로 AirSim을 작성하라는 메시지가 표시되면 예를 선택하십시오. 이제 시간이 좀 걸릴 수 있으니 커피 마시러 가세요 :).
6. Unreal 에디터에서 Play 버튼을 누르면 아래와 같은 비디오가 나타납니다. [AirSim 사용법](https://github.com/Microsoft/AirSim/#how-to-use-it)을 참조하십시오.

[![Blocks Demo Video](images/blocks_video.png)](https://www.youtube.com/watch?v=-r_QGaxMT4A)

### 코드 변경 및 다시 빌드 하기
Linux의 경우 AirLib 또는 Unreal/Plugins 폴더에서 코드를 변경한 다음 `./build.sh`를 실행하여 다시 빌드하십시오. 이 단계는 또한 빌드 출력을 Blocks 샘플 프로젝트에 복사합니다. 그런 다음 위 단계를 다시 수행하여 다시 실행할 수 있습니다.

## 차량 선택 : 자동차 또는 멀티 로터
기본적으로 AirSim은 멀티 로터를 생성합니다. 이것을 자동차로 쉽게 변경하고, 모든 AirSim 제품 사용할 수 있습니다. [자동차 사용](using_car.md) 가이드를 참조하십시오.

## FAQ
#### "_BuitData" 파일이 누락되었습다 와 같은 경고가 나타납니다.
이것들은 중간 파일이므로 무시해도 됩니다.
