# PX4/MavLink Logging

이 페이지에서 언급 한 PX4/MavLink 로깅을 위한 다양한 도구를 개발한 [Chris Lovett](https://github.com/clovett)에게 감사드립니다!

## Logging MavLink Messages

다음 명령은 MavLinkTest 앱을 시뮬레이터에 연결하고 PX4와의 모든 mavlink 명령 로깅을 활성화 합니다.
````
MavLinkTest -server:127.0.0.1:14550 -logdir:d:\temp
````

때로는 PX4로 전송되는 시뮬레이터에서 "output"을 기록하려고 할 수도 있습니다.
특히 이것은 시뮬레이터가 생성한 "hilgps" 및 "hilsensor" 메시지를 캡처합니다.
이를 수행하려면 다음을 수행하십시오:
````
MavLinkTest -server:127.0.0.1:14389 -logdir:d:\temp\output
````

그러면 d:\temp\logs에 날짜별로 구성된 로그 파일, 특히 *input.mavlink 및 *output.mavlink 파일이 표시됩니다.

## MavLink LogViewer
MavLink 지원 드론의 경우 [로그 뷰어](log_viewer.md)를 사용하여 데이터 스트림을 시각화 할 수도 있습니다.

## PX4 Log in SITL Mode

SITL 모드에서는 드론이 장착 될 때 로그 파일이 생성됩니다. SITL 터미널에는 다음과 같이 로그 파일의 경로가 포함되어 있습니다.
```
INFO  [logger] Opened log file: rootfs/fs/microsd/log/2017-03-27/20_02_49.ulg
```

## PX4 Log in SITL Mode

HIL 모드에서 Pixhawk 하드웨어를 사용하는 경우 QGroundControl을 사용하여 `SYS_LOGGER=1`로 설정하십시오. PX4는 QGroundControl을 사용하여 나중에 다운로드 할 수 있는 장치에 로그 파일을 작성합니다.
