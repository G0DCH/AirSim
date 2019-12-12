# Hello Drone

## Hello Drone은 어떻게 작동합니까?

Hello Drone은 RPC 클라이언트를 사용하여 AirSim에 의해 자동으로 시작되는 RPC 서버에 연결합니다.
RPC 서버는 모든 명령을 [MultirotorApiBase](https://github.com/Microsoft/AirSim/tree/master/AirLib//include/vehicles/multirotor/api/MultirotorApiBase.hpp)를 구현하는 클래스로 라우팅합니다. 본질적으로 MultirotorApiBase는 쿼드 로터에서 데이터를 가져오고 명령을 다시 보내는 추상 인터페이스를 정의합니다. 현재 MavLink 기반 차량용 MultirotorApiBase를 구체적으로 구현했습니다. DJI 드론 플랫폼, 특히 매트릭스의 구현이 작동 중입니다.
