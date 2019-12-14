# Linux에서 cmake 설치

cmake 버전 3.10이 없는 경우(예: Ubuntu 14에서 3.2.2가 기본값 임) 다음을 실행할 수 있습니다.

````
mkdir ~/cmake-3.10.2
cd ~/cmake-3.10.2
wget https://cmake.org/files/v3.10/cmake-3.10.2-Linux-x86_64.sh
````

이제 이 명령(상호작용 명령임)을 단독으로 실행해야 합니다.
````
sh cmake-3.10.2-Linux-x86_64.sh --prefix ~/cmake-3.10.2
````

다른 cmake-3.10.2-Linux-x86_64 폴더 생성에 관한 질문에 'n'을 대답한 다음
````
sudo update-alternatives --install /usr/bin/cmake cmake ~/cmake-3.10.2/bin/cmake 60
````

이제 cmake 버전이 3.10.2인지 확인하려면 `cmake --version`을 입력하십시오.