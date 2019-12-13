# Unreal Engine 4.18로 업그레이드

이 지침은 이미 Unreal Engine 4.16에서 AirSim을 사용중인 경우에 적용됩니다. AirSim을 설치 한 적이 없다면, [설치 방법](https://github.com/microsoft/airsim#how-to-get-it)을 참조하십시오.

**주의:** 아래 단계는 AirSim 또는 Unreal 폴더에 저장하지 않은 작업을 삭제합니다.

## 먼저 실행할 것

### Windows 유저 용
1. Visual Studio 2017과 VC++, Python 및 C#를 설치하십시오.
2. Epic Games Launcher를 통해 UE 4.18 설치하십시오.
3. `x64 Native Tools Command Prompt for VS 2017`를 실행하고 AirSim 리포지토리로 이동하십시오.
4. `clean_rebuild.bat`를 실행하여 체크하지 않은/추가 된 모든 것을 제거하고 모든 것을 다시 빌드하십시오.

### Linux 유저 용
1. AirSim repo 폴더에서 `clean_rebuild.sh`를 실행하십시오.
2. Unreal Engine의 기존 폴더 이름을 바꾸거나 삭제하십시오.
3. 1단계와 2단계를 따라 [Unreal Engine 4.18 설치](https://github.com/Microsoft/AirSim/blob/master/docs/build_linux.md#install-and-build).

## 커스텀 Unreal 프로젝트 업그레이드
이전 버전의 Unreal Engine에서 생성 한 고유 한 Unreal 프로젝트가있는 경우 프로젝트를 Unreal 4.18로 업그레이드해야합니다. 다음을 수행하십시오, 

1. .uproject 파일을 열고 `"EngineAssociation"` 행을 찾아서 `"EngineAssociation":"4.18"`과 같은지 확인하십시오.
2. Unreal 프로젝트 폴더에서 `Plugins/AirSim` 폴더를 삭제하십시오.
3. AirSim 리포지토리 폴더로 이동하여 `Unreal\Plugins` 폴더를 Unreal 프로젝트 폴더로 복사하십시오.
4. `Unreal\Environments\Blocks`에서 *.bat(Linux의 경우 *.sh)를 프로젝트 폴더로 복사하십시오.
5. `clean.bat`(Linux의 경우`clean.sh`) 다음에 `GenerateProjectFiles.bat`(Windows 전용)을 실행하십시오.

## FAQ

### 4.16보다 오래된 Unreal 프로젝트가 있습니다. 어떻게 업그레이드합니까?

#### 옵션 1 : 프로젝트를 재 작성
프로젝트에 다운로드한 환경 이외의 코드나 에셋이 없는 경우 간단히 [Unreal 4.18 Editor에서 프로젝트를 다시 생성](unreal_custenv.md)하고 `AirSim/Unreal/Plugins`에서 플러그인 폴더를 복사하면 됩니다.

#### 옵션 2 : 몇 개의 파일 수정
Unreal 4.15 이전의 Unreal 버전에는 큰 변화가 있었습니다. 따라서 Unreal 프로젝트의 `Source` 폴더에 있는 *.Build.cs 및 *.Target.cs를 수정해야 합니다. 그렇다면 그 변화는 무엇입니까? 아래는 그 요점이지만 실제로 [Unreal의 공식 4.16 전환 게시물](https://forums.unrealengine.com/showthread.php?145757-C-4-16-Transition-Guide)을 참조해야합니다.

##### 프로젝트의 *.Target.cs에서
1. 생성자를 `public MyProjectTarget(TargetInfo Target)` 에서 `public MyProjectTarget(TargetInfo Target) : base(Target)`으로 변경하십시오.

2. `SetupBinaries` 메소드가 있다면 위의 생성자에 다음 줄을 추가하십시오: `ExtraModuleNames.AddRange(new string[] { "MyProject" });`

##### 프로젝트의 *.Build.cs에서
생성자를 `public MyProject(TargetInfo Target)`에서 `public MyProject(ReadOnlyTargetRules Target) : base(Target)`으로 변경하십시오.

##### 그리고 마지막으로...
업그레이드를 계속하려면 위 단계를 따르십시오. 경고 박스에는 "복사본 열기" 버튼만 표시 될 수 있습니다. 클릭하지 마십시오. 대신 추가 옵션을 클릭하면 더 많은 버튼이 표시됩니다. `Convert-In-Place option`을 선택하십시오.
*주의 :* 항상 프로젝트 백업을 먼저 유지하십시오! 충돌하는 것이 없다면, 그 자리에서 변환이 진행되고 이제 새로운 버전의 Unreal에 있습니다.
