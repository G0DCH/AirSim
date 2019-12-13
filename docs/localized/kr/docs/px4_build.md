# PX4 빌드

## 소스 코드

PX4 소스 코드를 얻는 것은 쉽습니다:
````
git clone https://github.com/PX4/Firmware.git
cd Firmware
````

아직 git이 없다면 이것을 실행하십시오:

````
sudo apt-get install git
````

현재 1.6.0rc1 버전을 사용하여 테스트하고 있지만 최신 마스터 브랜치도 괜찮습니다.
이제 그것을 빌드하려면 올바른 도구가 필요합니다.

## PX4 빌드 도구

전체 지침은 [dev.px4.io](http://dev.px4.io/starting-installing-linux.html) 웹 사이트에서 볼 수 있지만 편의를 위해 해당 지침의 관련 서브셋을 여기에 복사했습니다.

([BashOnWindows](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide)를 사용하여 PX4 펌웨어를 빌드 할 수 있습니다. 이 페이지 하단의 BashOnWindows 지침을 따르십시오)

## SITL version 빌드

이제 위에서 만든 펌웨어 폴더에서 posix에서 실행되는 SITL 버전을 만들 수 있습니다:
````
make posix_sitl_ekf2
````

참고: 이 빌드 시스템은 매우 특별합니다 .git 서브 모듈(그리고 거기에 있는 많은 것들)을 업데이트하는 방법을 알고,
cmake(필요한 경우)를 실행 한 다음 빌드 자체를 실행합니다. 루트 Makefile은 meta-meta makefile 파일입니다. :-) 

약 2분도 채 걸리지 않습니다. 모두 성공하면 마지막 줄은 `px4` 앱을 연결하고, 다음을 사용하여 실행할 수 있습니다:

````
make posix_sitl_ekf2  none_iris
````

그리고 다음과 같은 출력이 표시됩니다:

````
creating new parameters file
creating new dataman file

______  __   __    ___ 
| ___ \ \ \ / /   /   |
| |_/ /  \ V /   / /| |
|  __/   /   \  / /_| |
| |     / /^\ \ \___  |
\_|     \/   \/     |_/

px4 starting.

18446744073709551615 WARNING: setRealtimeSched failed (not run as root?)
ERROR [param] importing from 'rootfs/eeprom/parameters' failed (-1)
Command 'param' failed, returned 1
  SYS_AUTOSTART: curr: 0 -> new: 4010
  SYS_MC_EST_GROUP: curr: 2 -> new: 1
INFO  [dataman] Unkown restart, data manager file 'rootfs/fs/microsd/dataman' size is 11797680 bytes
  BAT_N_CELLS: curr: 0 -> new: 3
  CAL_GYRO0_ID: curr: 0 -> new: 2293768
  CAL_ACC0_ID: curr: 0 -> new: 1376264
  CAL_ACC1_ID: curr: 0 -> new: 1310728
  CAL_MAG0_ID: curr: 0 -> new: 196616

````

이 방법은 좋습니다. 첫 번째 실행은 SITL 모드에 대한 px4 파라미터를 설정합니다. 두 번째 실행은 출력이 적습니다.
이 앱은 또한 명령을 입력 할 수있는 대화식 콘솔입니다. 그들이 무엇인지 보려면 'help'를 입력하고,
ctrl-C를 입력하여 도움말을 종료하십시오. 언제든지 다시 시작할 수 있습니다. 
필요한 경우 wonky state를 재설정 하는 것이 좋은 방법입니다(Pixhawk 하드웨어 재부팅과 동일함).

## ARM 임베디드 툴

실제 Pixhawk 하드웨어 용 PX4 펌웨어를 구축하려면 ARM Cortex-M4 칩셋 용 gcc 크로스 컴파일러가 필요합니다.
이 컴파일러는 PX4 DevGuide로 구할 수 있으며, 특히 `ubuntu_sim_nuttx.sh` 설정 스크립트에 있습니다.

이러한 설정 지침을 수행 한 후 `arm-none-eabi-gcc --version` 명령을 입력하여 설치를 확인할 수 있습니다. 다음과 같은 결과가 나타납니다:
````
arm-none-eabi-gcc (GNU Tools for Arm Embedded Processors 7-2017-q4-major) 7.2.1 20170904 (release) [ARM/embedded-7-branch revision 255204]
Copyright (C) 2017 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
````

## ARM 하드웨어에서 PX4 빌드

이제 실제 pixhawk 하드웨어에서 실행하기 위해 PX4 펌웨어를 빌드 할 수 있습니다:

````
make px4fmu-v2_default
````

이 빌드는 NuttX 실시간 OS, Pixhawk 비행 컨트롤러의 센서에 대한 모든 드라이버 등을 포함하여 훨씬 더 많이 빌드하므로 시간이 조금 더 걸립니다. 또한 컴파일러를 대용량 압축 모드로 실행하여 1MB ROM에 모든 것을 맞출 수 있습니다!!

좋은 tid 비트 하나는 pixhawk USB를 연결하고`make px4fmu-v2_default upload`를 입력하여 새로운 비트로 하드웨어를 플래시 할 수 있으므로 QGroundControl을 사용할 필요가 없다는 것입니다.

## 유용한 매개 변수

PX4에는 많은 사용자 정의 가능한 매개 변수가 있으며 (실제로 700 개 이상) AirSim으로 최상의 결과를 얻으려면 다음 매개 변수가 유용하다는 것을 알았습니다:
````
// 새 위치 추정기 모듈을 활성화 해야 합니다:
param set SYS_MC_EST_GROUP 2

// 크루즈 속도의 기본 제한을 높이면 큰 맵을 더 빠르게 이동할 수 있습니다.
param MPC_XY_CRUISE 10             
param MPC_XY_VEL_MAX 10
param MPC_Z_VEL_MAX_DN 2

// 착륙시 자동 해제에 대한 시간 초과를 늘려 장기 실행 앱이 걱정할 필요가 없습니다.
param COM_DISARM_LAND 60

// 무선 조종 장치를 부착하지 않고 비행 할 수 있도록 합니다(실제 드론에서는이 작업을 수행하지 마십시오).
param NAV_RCL_ACT 0

// 새로운 syslogger가 PX4 로그에서 더 많은 정보를 얻도록 설정
param set SYS_LOGGER 1
````

## BashOnWindows 사용

[Bash on Windows Toolchain](https://dev.px4.io/en/setup/dev_env_windows_bash_on_win.html)를 보십시오.