# Log Viewer

LogViewer는 Unreal Simulator에서 가져 오는 MavLink 스트림을 표시하는 Windows WPF 앱입니다.
이를 사용하여 드론이 비행하는 동안 발생하는 상황을 모니터링 할 수 있습니다.
예를 들어, 아래 그림은 시뮬레이터에 의해 생성 된 x, y 및 z 자이로 센서 정보의 실시간 그래프를 보여줍니다.

### Usage

이 LogViewer를 사용하려면 시뮬레이션을 실행하기 `전에` 이것을 시뮬레이터에 연결하십시오. 
창의 오른쪽 상단에 있는 파란색 커넥터 버튼을 누르고 소켓 '탭'을 선택하고 포트 번호 14388과 'localhost' 네트워크를 입력하십시오.
그런 다음 녹화 버튼을 누릅니다(툴바의 오른쪽에 있는 삼각형).
이제 시뮬레이터를 시작하고 그래프로 만들 mavlink 항목을 선택하십시오. 다음과 같이 보일 것입니다.

![Log Viewer](images/log_viewer.png)

여기의 드론 뷰는 PX4에서 나오는 실제 예상 위치이므로 PX4가 시뮬레이터와 동기화되어 있는지 확인하는 좋은 방법입니다.
때때로 상태 추정이 실제 상태를 따라 잡을 때 여기에서 약간의 드리프트를 볼 수 있습니다. 이는 bad crash 후에 더 잘 보입니다.

### 설치

LogViewer.sln을 빌드 할 수 없는 경우 [click once installer](http://www.lovettsoftware.com/LovettSoftware/Downloads/Px4LogViewer/Px4LogViewer.application)도 있습니다.

### 구성

마법의 포트 번호 14388은 [settings.json 파일](settings.md)을 편집하여 시뮬레이터에서 구성 할 수 있습니다.

