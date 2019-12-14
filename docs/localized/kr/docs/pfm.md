# pfm 형식

Pfm(또는 Portable FloatMap) 이미지 형식은 이미지를 부동 소수점 픽셀로 저장하므로 일반적인 0-255 픽셀 값 범위로 제한되지 않습니다. 깊이와 같은, 색상 이외의 것을 묘사하는 HDR 이미지 또는 일반 이미지에 유용합니다.

이 파일 형식을 보기에 좋은 뷰어 중 하나는 [PfmPad](https://sourceforge.net/projects/pfmpad/)입니다. Maverick 사진 뷰어는 깊이 이미지가 제대로 표시되지 않으므로 권장하지 않습니다.

AirSim에는 [C++](https://github.com/Microsoft/AirSim/blob/master/AirLib/include/common/common_utils/Utils.hpp#L637)에서 pfm 파일을 작성하고 [Python](https://github.com/Microsoft/AirSim/tree/master/PythonClient//airsim/utils.py#L122)에서 작성하고 읽을 수 있는 코드가 있습니다.
