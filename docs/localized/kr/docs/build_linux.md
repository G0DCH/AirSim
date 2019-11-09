# Linux에서 AirSim 빌드

현재 테스트 된 환경인 **Ubuntu 16.04 LTS**을 권장합니다. 이론적으로 다른 배포판과 OSX에서도 빌드 할 수 있지만, 우리는 테스트하지 않았습니다.

두 가지 옵션이 있습니다 - Docker 컨테이너 또는 Host Machine 내부에 빌드 할 수 있습니다.

## Docker 
[이 지침](https://github.com/Microsoft/AirSim/blob/master/docs/docker_ubuntu.md)을 참조하십시오.

## Host machine

### Unreal Engine 과 Airsim 빌드
- 당신이 [Epic Games 계정이 있는지](https://docs.unrealengine.com/latest/INT/Platforms/Linux/BeginnerLinuxDeveloper/SettingUpAnUnrealWorkflow/1/index.html) 확인하십시오. Unreal Engine에 대한 소스 코드 액세스를 위해 필요합니다.

- 원하는 폴더에 Unreal을 복제하고 빌드하십시오(시간이 걸릴 수 있습니다!). **참고** : 현재는 Unreal 4.18 만 지원합니다.
   ```bash
   # GitHub 프로젝트를 복제 한 폴더로 이동
   git clone -b 4.18 https://github.com/EpicGames/UnrealEngine.git
   cd UnrealEngine
   ./Setup.sh
   ./GenerateProjectFiles.sh
   make
   ```

- AirSim을 복제하고 빌드:
   ```bash
   # GitHub 프로젝트를 복제 한 폴더로 이동
   git clone https://github.com/Microsoft/AirSim.git
   cd AirSim

  AirSim은 clang 5를 사용하여 바이너리를 빌드 할 것을 권장합니다. 바이너리는 Unreal과 호환됩니다. 
  setup 스크립트는 올바른 버전의 cmake, llvm 및 eigen을 설치합니다:
   ./setup.sh
   ./build.sh
   ```

  추가로, 다른 이유로 GCC 바이너리가 필요한 경우 다음과 같이 설정에 gcc를 추가하고 호출을 빌드 할 수 있습니다:
   ```bash
   ./setup.sh --gcc
   ./build.sh --gcc
   ```

### Unreal 환경 빌드

마지막으로, 차량 환경을 호스팅하는 Unreal 프로젝트가 필요합니다. AirSim에는 여러분이 사용할 수 있는, 혹은 여러분만의 환경을 만들 수 있는 "Blocks Environment"가 내장되어 있습니다. [Unreal 환경 설정](unreal_proj.md)을 참조하십시오.

### [선택] 원격 컨트롤러 설치 (Multirotor 만 해당)

수동으로 비행하려면 원격 컨트롤러가 필요합니다. 자세한 사항은 [원격 컨트롤러 설정](remote_control.md)을 참조하십시오.

또는 프로그램적 조종을 위해 [API](apis.md)를 사용하거나 키보드를 사용하여 소위 [Computer Vision 모드](image_apis.md)를 사용하여 주변을 이동할 수 있습니다.

## AirSim 사용법

위 단계에 따라 AirSim을 설정하면 다음을 수행 할 수 있습니다.

- `UnrealEngine` 폴더로 이동하고 `UnrealEngine/Engine/Binaries/Linux/UE4Editor`을 실행시켜 Unreal을 시작하세요.
- Unreal Engine이 프로젝트를 열거나 생성하라는 메시지를 표시하면 찾아보기를 선택하고 `AirSim/Unreal/Environments/Blocks`(또는 나만의 [커스텀](unreal_custenv.md) Unreal 프로젝트)를 선택하십시오.
- 프로젝트를 변환하라는 메시지가 표시되면 추가 옵션 또는 이 위치에서 변환 옵션을 찾으십시오. 빌드하라는 메시지가 표시되면 예를 선택하십시오. AirSim 플러그인을 비활성화하라는 메시지가 나타나면 아니오를 선택하십시오.
- Unreal 에디터가 로드 된 후, Play 버튼을 누르세요. 팁: '편집->에디터 개인설정'으로 가서, '검색'창에 'CPU'를 타이핑 하고 'Use Less CPU when in Background'이 체크 해제 되었는지 확인하세요.

다양한 사용 가능한 옵션에 대해서는 [API 사용](apis.md) 및 [settings.json](settings.md)을 참조하십시오.

## FAQ

- "<MyProject> could not be compiled. Try rebuilding from source manually" 에러가 발생했습니다.
  * 컴파일 오류 또는 gch 파일이 오래되어서 발생할 수 있습니다. 콘솔 창을 확인하세요. 아래와 같은 메시지가 보입니까?
   ```
   fatal error: file  '/usr/include/linux/version.h''/usr/include/linux/version.h'  has  been  modified  since  the  precompiled  header
   ```
  * 이 경우 해당 메시지 다음에 나오는 * .gch 파일을 찾아 삭제 한 후 다시 시도하십시오. Unreal Engine 포럼에 [관련 스레드](https://answers.unrealengine.com/questions/412349/linux-ue4-build-precompiled-header-fatal-error.html)가 있습니다.
  * 콘솔에 다른 컴파일 오류가 표시되면 해당 소스 파일을 열고 변경 사항이 원인인지 확인하십시오. 그렇지 않은 경우 GitHub Issue란에 보고하십시오.

- Unreal이 크래시 됐습니다! 무엇이 잘못되었는지 어떻게 알 수 있습니까?
  * `MyUnrealProject/Saved/Crashes` 폴더로 가서 서브 디렉토리에서 `MyProject.log` 파일을 검색하십시오. 이 파일의 끝에 스택 추적 및 메시지가 표시됩니다.    
   `Diagnostics.txt` 파일을 살펴볼 수도 있습니다.

- Linux에서 IDE를 어떻게 사용합니까?
  * Qt Creator 또는 CodeLite를 사용할 수 있습니다. [여기](https://docs.unrealengine.com/latest/INT/Platforms/Linux/BeginnerLinuxDeveloper/SettingUpAnIDE/index.html)서 Qt Creator에 대한 지침을 사용할 수 있습니다.

- Windows 시스템에서 Linux 용 크로스 컴파일을 할 수 있습니까?
  * 예, 가능하지만 우리는 테스트해보지 않았습니다. [여기](https://docs.unrealengine.com/latest/INT/Platforms/Linux/GettingStarted/index.html)서 지침을 찾을 수 있습니다.

- AirSim은 어떤 컴파일러와 표준 라이브러리를 사용합니까?
  * 우리는 컴파일러는 **Clang 5.0**, 표준 라이브러리는 **libc++**로 Unreal Engine이 사용하는 것과 동일한 컴파일러를 사용합니다. AirSim의 `setup.sh`는 둘 다 자동으로 다운로드합니다. libc++ 소스 코드는`llvm-source-(version)` 폴더에 복제되고 CMake가 libc++를 사용하는 `llvm-build` 폴더에 빌드됩니다.

- AirSim 빌드는 어떤 버전의 CMake를 사용합니까?
  * 3.9.0 이상입니다. 이것은 우분투 16.04의 기본값이 *아니므로* setup.sh가 자동으로 설치합니다. `cmake --version`을 사용하여 CMake 버전을 확인할 수 있습니다. 이전 버전이있는 경우 [이 지침](cmake_linux.md)을 따르거나 [CMake 웹 사이트](https://cmake.org/install/)를 참조하십시오.

- BashOnWindows에서 AirSim을 컴파일 할 수 있습니까?
  * 네, 하지만 BashOnWindows에서는 Unreal을 실행할 수 없습니다. 따라서 Linux 컴파일을 확인하는 데 유용하지만 end-to-end 실행에는 유용하지 않습니다.    
   [BashOnWindows 설치 안내서](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide)를 참조하세요.   
   이전 버전에는 다양한 문제가 있었으므로 최신 버전(Windows 10 Creators Edition)이 있어야합니다.
   또한 Visual Studio 명령 프롬프트에서 bash를 호출하지 마십시오. 그렇지 않으면 CMake가 VC ++를 찾아서 사용할 수 있습니다!

- Linux에서 Unreal을 실행하는 것에 대한 자세한 정보는 어디서 찾을 수 있습니까?
   * 여기에서 시작하십시오 : [Linux에서의 Unreal](https://docs.unrealengine.com/latest/INT/Platforms/Linux/index.html)
   * [Linux에서 Unreal 빌드](https://wiki.unrealengine.com/Building_On_Linux#Clang)
   * [Unreal의 Linux 지원](https://wiki.unrealengine.com/Linux_Support)
   * [Unreal 크로스 컴파일](https://wiki.unrealengine.com/Compiling_For_Linux)
