# 개발 워크 플로우

다음은 AirSim으로 작업하는 동안 다양한 개발 활동을 수행하는 방법에 대한 가이드입니다. Unreal Engine 기반 프로젝트를 처음 사용하고, AirSim에 기여하거나 내 요구를 위한 고유한 fork를 만들려는 경우, 시간이 절약 될 수 있습니다.

## 개발 환경
### OS
개발 환경으로 Windows 10 및 Visual Studio 2017을 적극 권장합니다. 다른 OS와 IDE에 대한 지원은 불행하게도 Unreal Engine 쪽에서 원활하지 않으며, 당신은 해결책을 찾으려 노력하는 등 심각한 생산성 손실을 초래할 수 있습니다.

### 하드웨어
64GB RAM, 6개 이상의 코어, SSD 및 2-3개의 디스플레이(이상적으로는 4K), NVidia 1080 또는 NVidia Titan 시리즈와 같은 GPU를 갖춘 강력한 데스크톱을 권장합니다. HP Z840은 우리의 요구에 매우 적합합니다. 하이엔드 노트북은 일반적으로 강력한 데스크톱에 비해 하위 수준이지만, 비상시에 유용할 수 있습니다. 일반적으로 64GB RAM, SSD 및 4K 디스플레이를 갖춘 개별 NVidia GPU(최소 M2000 이상)의 노트북을 원합니다. Lenovo P50과 같은 모델은 우리의 요구에 적합합니다. 통합 그래픽만 있는 노트북은 제대로 작동하지 않을 수 있습니다.

## AirSim 코드 업데이트 및 변경

### 개요
AirSim은 플러그인으로 디자인되었습니다. 즉, 단독으로 실행할 수 없으므로 Unreal 프로젝트에 배치해야 합니다(우리는 "환경" 이라고 부릅니다). AirSim의 구축 및 테스트에는 두 단계가 있습니다. (1) 플러그인 빌드 (2) Unreal 프로젝트에 플러그인을 배포하고 프로젝트를 실행하기 

첫 번째 단계는 AirSim 루트 디렉토리에서 사용 가능한 build.cmd에 의해 수행됩니다. 이 명령은 `Unreal\Plugins` 폴더에서 플러그인에 필요한 모든 것을 업데이트합니다. 플러그인을 배포하려면 `Unreal\Plugins` 폴더를 Unreal 프로젝트 폴더에 복사하면됩니다. 다음으로 Unreal 프로젝트에서 모든 중간 파일을 제거한 다음, Unreal 프로젝트의 .sln 파일을 재생성해야 합니다. 이를 위해 `Unreal\Environments\Blocks` 폴더에는 `clean.bat` 와 `GenerateProjectFiles.bat`라는 두 개의 편리한 .bat 파일이 있습니다. 따라서 Unreal 프로젝트의 루트 디렉토리에서 이 bat 파일을 순서대로 실행하십시오. 이제 Visual Studio에서 새로운 .sln 파일을 열고 F5를 눌러 실행할 수 있습니다.

### 단계
다음은 AirSim을 변경하고 테스트하기 위해 사용하는 단계입니다. AirSim 코드로 개발하는 가장 좋은 방법은 [Blocks 프로젝트](unreal_blocks.md)를 사용하는 것입니다. 이것은 가벼운 프로젝트이므로 컴파일 시간이 비교적 빠릅니다. 일반적으로 워크 플로우는 다음과 같습니다.

```
REM //x64 Native Tools Command Prompt for VS 2017 사용
REM //AirSim 리포지토리 폴더로 이동

git pull                          
build.cmd                        
cd Unreal\Environments\Blocks         
update_from_git.bat
start Blocks.sln
```

위의 명령은 먼저 AirSim 플러그인을 빌드 한 다음, 편리한 `update_from_git.bat`를 사용하여 Blocks 프로젝트에 배포합니다. 이제 Visual Studio 솔루션 내부에서 작업하고 코드를 변경 한 다음 F5를 눌러 변경 사항을 빌드, 실행 및 테스트 할 수 있습니다. 디버깅, 중단점 등은 평소와 같이 작동해야 합니다.

코드 변경 작업을 마친 후에는, 변경 사항을 AirSim 리포지토리 또는 자신의 fork로 push하거나 새 플러그인을 나만의 Unreal 프로젝트에 배포 할 수 있습니다. 이렇게 하려면 명령 프롬프트로 돌아가서 AirSim 리포지토리 폴더를 먼저 업데이트 하십시오:


```
REM //x64 Native Tools Command Prompt for VS 2017 사용
REM //Unreal\Environments\Blocks 에서 이것을 실행

update_to_git.bat
build.cmd
```

위의 명령은 코드 변경 사항을 Unreal 프로젝트 폴더에서 다시 `Unreal\Plugins` 폴더로 전송합니다. 이제 변경 사항을 AirSim 리포지토리 또는 자신의 fork로 push 할 준비가 되었습니다. `Unreal\Plugins`를 나만의 Unreal engine 프로젝트에 복사하고 모든 것이 커스텀 프로젝트에서도 작동하는지 확인할 수 있습니다.

### Take Away
 Unreal 빌드 시스템 및 플러그인 모델의 작동 방식과 위 단계를 수행 한 이유를 이해했다면 이 워크 플로우를 따르는 것이 매우 편합니다. .bat 파일을 열어서 내부를 들여다보는 것을 두려워하지 말고 그것이 무엇을 하는지 보십시오. 그것들은 아주 작고 간단합니다(물론 build.cmd는 보지 마십시오).

## FAQ

#### Blocks 프로젝트에서 코드를 변경했지만 작동하지 않습니다.
Visual Studio에서 F5 또는 F6을 눌러 빌드를 시작하면 Unreal 빌드 시스템이 시작되어 더티 파일과 빌드해야 하는 파일이 있는지 확인합니다. 불행하게도, Unreal 헤더와 객체 계층을 사용하는 코드가 아닌 더티 파일을 인식하지 못하는 경우가 종종 있습니다. 트릭은 Unreal 빌드 시스템이 항상 인식하는 일부 파일을 더티 상태로 만드는 것입니다. 우리가 가장 좋아하는 것은 AirSimGameMode.cpp입니다. 행을 삽입하고 삭제 한 후 파일을 저장하십시오.

#### Visual Studio 외부에서 코드를 변경했지만 작동하지 않습니다.
하지 마십시오! Unreal 빌드 시스템은 Visual Studio를 사용한다고 *가정*하고 Visual Studio와 통합하기 위해 많은 작업을 수행합니다. 다른 편집기를 사용해야 하는 경우, Unreal 프로젝트에서 커맨드 라인 빌드를 수행하는 방법을 찾아보거나 편집기에서 커맨드 라인 빌드를 Unreal 빌드 시스템과 통합할 수 있는 방법에 대한 문서를 참조하거나 `clean.bat` + `GenerateProjectFiles.bat`를 실행하여 VS 솔루션이 동기화되었는지 확인합니다.

#### Unreal 프로젝트에 새 파일을 추가하려고 하는데 작동하지 않습니다.
그렇지 않습니다! 실제로 Visual Studio 솔루션 파일을 사용하는 동안 이 솔루션 파일은 실제로 Unreal 빌드 시스템에서 생성되었다는 것을 기억하십시오. 프로젝트에 새 파일을 추가하려면 먼저 Visual Studio를 종료하고 원하는 위치에 빈 파일을 추가 한 다음 `GenerateProjectFiles.bat`를 실행하여 프로젝트의 모든 파일을 스캔 한 다음 .sln 파일을 다시 만듭니다. 이제 이 새 .sln 파일을 열면 당신은 준비가 됐습니다.

#### Unreal\Plugins 폴더를 복사했지만 Unreal 프로젝트에서 아무 것도 발생하지 않습니다.
먼저 프로젝트의 .uproject 파일이 플러그인을 참조하고 있는지 확인하십시오. 그런 다음 위의 개요에서 설명한대로 `clean.bat` 및 `GenerateProjectFiles.bat`를 실행했는지 확인하십시오.

#### AirSim 플러그인을 사용하는 여러 개의 Unreal 프로젝트가 있습니다. 쉽게 업데이트하려면 어떻게 해야합니까?
운이 좋습니다! 정확히 그 일을 하는 `build_all_ue_projects.bat`가 있습니다. 블랙 박스(적어도 아직은 아님)로 취급하지 말고 열어서 그 기능을 확인하십시오. 커맨드 라인 인자에서 설정되는 4 개의 변수가 있습니다. 이러한 인자가 제공되지 않으면 다음 명령문 세트에서 기본값으로 설정됩니다. 경로의 기본 값을 변경하려고 할 수 있습니다. 이 batch 파일은 AirSim 플러그인을 구축하여 나열된 모든 프로젝트에 배포하고(batch 파일의 뒷부분에 있는 CALL 문 참조) 이러한 프로젝트에 대한 패키지를 실행하고 지정된 폴더에 최종 바이너리 파일을 넣습니다 - 이 모든 작업은 한 번에 수행됩니다! 이것이 바로 우리만의 바이너리 릴리즈를 만들기 위해 사용하는 것입니다.

#### AirSim에 어떻게 기여합니까?
변경하기 전에 당신의 branch를 만들었는지 확인하십시오. Blocks 환경에서 코드 변경 사항을 테스트 한 후 [일반적인 단계](https://akrabat.com/the-beginners-guide-to-contributing-to-a-github-project/)에 따라 다른 GitHub 프로젝트와 마찬가지로 기여하십시오. Git Branch-Rebase-Merge 워크 플로우에 익숙하지 않다면, [이것을 먼저 읽으십시오](http://shitalshah.com/p/git-workflow-branch-rebase-squash-merge/).



