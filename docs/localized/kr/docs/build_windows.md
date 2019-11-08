# Windows에서 AirSim 빌드

## Unreal Engine 설치

1. Epic Games Launcher를 [다운로드](https://www.unrealengine.com/download)하세요. Unreal Engine은 오픈 소스이며 무료로 다운로드 할 수 있지만 등록은 여전히 ​​필요합니다.
2. Epic Games Launcher를 실행하고 왼쪽 창에서 '라이브러리'탭을 엽니다.
'버전 추가'를 클릭하면 아래와 같이 **Unreal 4.18** 다운로드 옵션이 표시됩니다. 여러 버전의 Unreal이 설치되어있는 경우 버전의 실행 버튼 옆에있는 아래쪽 화살표를 클릭하여 **4.18이 `기본`으로 설정되어 있는지** 확인하십시오.

   **참고**: UE 4.16 이전 프로젝트가 있는 경우 [업그레이드 가이드](unreal_upgrade.md)를 참조하여 프로젝트를 업그레이드하십시오.

## AirSim 업그레이드
* Visual Studio 2017를 설치하세요.    
* VS 2017을 설치하는 동안 **VC ++** 및 **Windows SDK 8.1**을 선택해야 합니다.
* `VS 2017용 x64 네이티브 도구 명령 프롬프트`를 시작하세요. 
* 리포지토리를 복제하세요: `git clone https://github.com/Microsoft/AirSim.git`, 그리고 `cd AirSim`으로 AirSim 디렉토리로 이동하세요. 
* 명령어 라인에서 `build.cmd`을 실행하세요. 언리얼 프로젝트에 놓을 수 있는 `Unreal\Plugins` 폴더에 plugin bit를 사용할 준비가 됩니다.

## Unreal 프로젝트 빌드

마지막으로, 차량의 환경을 호스팅하는 Unreal 프로젝트가 필요합니다. AirSim에는 여러분이 사용할 수 있는, 혹은 여러분만의 환경을 만들 수 있는 "Blocks Environment"가 내장되어 있습니다. [Unreal 환경 설정](unreal_proj.md)을 참조하십시오.

## 원격 컨트롤러 설정 (Multirotor만 해당)

수동으로 비행하려면 원격 컨트롤러가 필요합니다. 자세한 사항은 [원격 제어 설정](remote_control.md)을 참조하십시오.

또는 프로그램적 조종을 위해 [API](apis.md)를 사용하거나 키보드를 사용하여 소위 [Computer Vision 모드](image_apis.md)를 사용하여 주변을 이동할 수 있습니다.

## AirSim 사용법

위 단계에 따라 AirSim을 설정하면 다음을 수행 할 수 있습니다.

1. .sln 파일(혹은 나만의 [커스텀](unreal_custenv.md) Unreal 프로젝트에 있는 .sln 파일)을 더블클릭 해서 `Unreal\Environments\Blocks`에 있는 Blocks 프로젝트를 불러오십시오. 당신의 .sln 파일이 표시되지 않는 경우에 당신은 아마 위의 Unreal 프로젝트 빌드 섹션 단계를 완료하지 않은 것입니다.
2. 당신의 Unreal 프로젝트(예: Blocks 프로젝트)를 시작 프로젝트로 선택하고, 빌드 구성이 "Develop Editor"및 x64로 설정되어 있는지 확인하십시오.
3. Unreal 에디터가 로드 된 후, Play 버튼을 누르세요. 팁: '편집->에디터 개인설정'으로 가서, '검색'창에 'CPU'를 타이핑 하고 'Use Less CPU when in Background'이 체크 해제 되었는지 확인하세요.

다양한 사용 가능한 옵션에 대해서는 [API 사용](apis.md) 및 [settings.json](settings.md)을 참조하십시오.

# Unity 위에서의 AirSim (실험적 릴리스)
[Unity](https://unity3d.com/)는 또 하나의 훌륭한 게임 엔진 플랫폼이며 AirSim의 Unity 위에서의 [실험적 릴리스](https://github.com/Microsoft/AirSim/tree/master/Unity)가 있습니다. 이 작업은 진행 중이며 일부 기능은 아직 작동하지 않을 수 있습니다.

# FAQ
#### build.cmd 실행 시 `error C100 : An internal error has occurred in the compiler`가 발생했습니다.
우리는 VS 버전 `15.9.0`에서 이런 일이 발생하는 것을 확인하고 AirSim 코드에서 해결 방법을 적용했습니다. 이 VS 버전을 사용하는 경우 최신 AirSim 코드를 가져와야 합니다.

#### "'corecrt.h': No such file or directory" or "Windows SDK version 8.1 not found"가 발생했습니다.
Visual Studio에 [Windows SDK](https://developercommunity.visualstudio.com/content/problem/3754/cant-compile-c-program-because-of-sdk-81cant-add-a.html)가 설치되어 있지 않을 가능성이 큽니다.

#### AirSim에서 PX4 펌웨어를 어떻게 사용합니까?
기본적으로 AirSim은 [simple_flight](simple_flight.md)라는 자체 내장 펌웨어를 사용합니다. 이것을 그냥 사용하기를 원하는 경우 추가 설정할 필요가 없습니다. PX4를 사용하도록 바꾸려면 [이 안내서](px4_setup.md)를 참조하십시오.

#### Visual Studio에서 코드를 변경했지만 효과가 없습니다.

헤더 파일 만 변경한 경우에 Unreal + VS 빌드 시스템이 재 컴파일하지 않는 경우가 있습니다. 다시 컴파일하려면 AirSimGameMode.cpp와 같은 Unreal 기반 cpp 파일을 "수정된 상태"로 만드십시오.

#### Unreal이 여전히 VS2015를 사용하거나 링크 오류가 발생합니다.
여러 버전의 VS를 실행하면 UE 프로젝트를 컴파일 할 때 문제가 발생할 수 있습니다. 그 문제는 UE가 작동하지 않을 수 있는 이전 버전의 VS로 컴파일을 시도하는 것입니다. Unreal에는 엔진과 프로젝트에 사용할 VS 버전을 바꾸기 위한 설정이 두 가지가 있습니다.
1. 편집 -> 에디터 개인설정 -> 일반 -> 소스 코드
2. 편집 -> 프로젝트 세팅 -> 플랫폼 -> Windows -> Toolchain -> CompilerVersion

경우에 따라 이러한 설정으로 원하는 결과가 나오지 않고 다음과 같은 오류가 발생할 수 있습니다: LINK : fatal error LNK1181: cannot open input file 'ws2_32.lib'

이러한 문제를 해결하기 위해 다음 절차를 적용 할 수 있습니다:
1. [VisualStudioUninstaller](https://github.com/Microsoft/VisualStudioUninstaller/releases)를 사용하여 이전 버전의 VS를 모두 제거하십시오.
2. VS2017를 복구/설치하십시오
3. 컴퓨터를 재시작하고 Epic launcher를 설치하고 원하는 엔진 버전을 설치하십시오.