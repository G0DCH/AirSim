# 최신 C++ 코딩 지침

우리는 Modern C++11을 사용하고 있습니다. 스마트 포인터, Lambdas 및 C++11 멀티 스레딩 프리미티브는 친구입니다.

## Quick Note

"표준"의 장점은 다음 중에서 선택할 수있는 것이 많다는 것입니다: [ISO](https://isocpp.org/wiki/faq/coding-standards), [Sutter &amp; Stroustrup](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md), [ROS](http://wiki.ros.org/CppStyleGuide), [LINUX](https://www.kernel.org/doc/Documentation/CodingStyle), [Google's](https://google.github.io/styleguide/cppguide.html), [Microsoft's](https://msdn.microsoft.com/en-us/library/888a6zcz.aspx), [CERN's](http://atlas-computing.web.cern.ch/atlas-computing/projects/qa/draft_guidelines.html), [GCC's](https://gcc.gnu.org/wiki/CppConventions), [ARM's](http://infocenter.arm.com/help/index.jsp?topic=/com.arm.doc.dui0475c/CJAJAJCJ.html), [LLVM's](http://llvm.org/docs/CodingStandards.html) 그리고 아마 수천가지의 것들.
불행히도 이들 중 대부분은 클래스 또는 상수의 이름을 지정하는 방법과 같은 기본적인 것에 동의 할 수도 없습니다. 아마도 이러한 표준은 기존 코드 기반을 지원하기 때문에 많은 레거시 문제가 발생하기 때문일 수 있습니다. 이 문서의 목적은 ISO, Sutter &amp; Stroustrup 및 ROS는 가능한 많은 충돌, 단점 및 불일치를 해결합니다.

## 명명 규칙

이름에 헝가리어 표기법을 사용하는 것과 포인터에 "_ptr"을 붙이지 마십시오.

| **코드 요소** | **스타일** | **Comment** |
| --- | --- | --- |
| Namespace | under\_scored | 클래스 이름과 차별화 |
| Class name | CamelCase | ISO가 권장하는 STL 유형과 구별하기 위해("C" 또는 "T" 접두어를 사용하지 마십시오) |
| Function name | camelCase | 소문자 시작은 .Net 세계를 제외하고 거의 보편적입니다. |
| Parameters/Locals | under\_scored | 표준의 대다수는 이것을 권장합니다. \_는 C++ 사용자가 더 읽기 쉽다 (Java/.Net 사용자는 아니지만) |
| Member variables | under\_scored\_with\_ | 접두사 \_는 ISO에 \_identifiers를 예약하는 규칙이 있으므로 크게 권장하지 않습니다. 대신 접미사를 권장합니다 |
| Enums and its members | CamelCase | 아주 오래된 표준을 제외한 대부분이 이것에 동의합니다 |
| Globals | g\_under\_scored | 당신은 처음부터 이것을 가지고 있지 않아야합니다! |
| Constants | UPPER\_CASE | 매우 논쟁적이어서, 클래스 또는 메소드의 private 상수가 아닌 경우 멤버 또는 로컬에 이름 지정을 사용하지 않는 한 여기에서 하나를 선택해야합니다. |
| File names | Match case of class name in file | 많은 장점과 단점이 있지만 자동 생성 코드의 불일치를 제거합니다(ROS에 중요) |

## 헤더파일

다중 참조를 방지하려면 네임 스페이스로 한정된 #ifdef를 사용하십시오.

```
#ifndef msr_airsim_MyHeader_hpp
#define msr_airsim_MyHeader_hpp

//--your code

#endif
```

#pragma once를 사용하지 않는 이유는 동일한 헤더 파일이 여러 위치에 있는 경우 지원되지 않기 때문입니다(ROS 빌드 시스템에서 가능할 수 있음).

## 중괄호

내부 함수 또는 메소드 본문은 동일한 행에 중괄호를 배치합니다.
그 밖의 네임 스페이스, 클래스 및 메소드 레벨은 별도의 행을 사용합니다.
이를 [K&amp;R 스타일](https://en.wikipedia.org/wiki/Indent_style#K.26R_style)이라고 하며 다른 언어에서 더 널리 사용되는 다른 스타일에 비해 이 변형은 C++에서 널리 사용됩니다.
단일 명령문이 있는 경우 중괄호가 필요하지 않지만 중괄호를 사용하여 복잡한 명령문을 올바르게 유지하는 것이 더 쉽습니다.

```
int main(int argc, char* argv[])
{
     while (x == y) {
        f0();
        if (cont()) {
            f1();
        } else {
            f2();
            f3();
        }
        if (x > 100)
            break;
    }
}
```

## Const and References

상수 및 참조 후보로 선언 한 모든 스칼라가 아닌 매개 변수를 엄격하게 검토하십시오. C#/Java/Python과 같은 언어에서 오는 경우 가장 자주하는 실수는 `const T&;` 대신 값으로 매개 변수를 전달하는 것입니다. 특히, 대부분의 문자열, 벡터 및 맵은 `constT&;`(읽기 전용 인 경우) 또는 `T&`(쓰기 가능한 경우)로 전달하려고 합니다. 또한 메소드에 `const` 접미사를 가능한 많이 추가하십시오.

## Overriding
가상 메소드를 오버라이드 할 때는 override 접미사를 사용하십시오.


## 포인터

이것은 실제로 메모리 관리에 관한 것입니다. 시뮬레이터는 성능에 중요한 코드가 많으므로 많은 new/delete 호출로 메모리 매니저 과부하가 걸리지 않도록 노력해야 합니다. 또한 스택에 매개 변수를 너무 많이 복사하지 않기를 원하므로 가능하면 참조로 변수를 전달합니다.
그러나 객체가 실제로 호출 스택보다 오래 살아야하는 경우 힙에 해당 객체를 할당해야 하므로 포인터를 사용해야 합니다. 이제 해당 개체의 수명 관리가 까다로울 경우 [C++ 11 스마트 포인터](https://cppstyle.wordpress.com/c11-smart-pointers/)를 사용하는 것이 좋습니다.
그러나 스마트 포인터는 비용이 많이 들기 때문에 맹목적으로 사용하지 마십시오.  성능이 가장 중요한 private 코드의 경우 raw 포인터를 사용할 수 있습니다. 소켓 API와 같이 포인터 유형 만 허용하는 레거시 시스템과 인터페이스 할 때 raw 포인터가 종종 필요합니다. 그러나 우리는 이러한 레거시 인터페이스를 가능한 한 많이 래핑하고 그러한 스타일의 프로그래밍이 더 큰 코드 기반으로 누출되는 것을 피하려고 합니다.

`const float * const xP`와 같이 어디에서나 const를 사용할 수 있는지 엄격히 확인하십시오. 변수 이름에 포인터 유형을 표시하기 위해 접두사 또는 접미사를 사용하지 마십시오. 예를 들어 변수를 더 잘 구별하는 것이 합리적일 경우를 제외하고는 `myobj_ptr` 대신 `my_obj`를 사용하십시오 (예 :`int mynum = 5; int * mynum_ptr = mynum;`

## 너무 짧죠, 그쵸?

그렇습니다. 아무도 200 페이지 코딩 지침을 읽는 것을 좋아하지 않기 때문에 의도된 것입니다. 여기서 목표는 [GCC의 strict mode 컴파일](http://shitalshah.com/p/how-to-enable-and-use-gcc-strict-mode-compilation/) 및 VC ++의 레벨 4 오류와 같은 경고로 아직 다루지 않은 가장 중요한 사항만 다루는 것입니다. C++로 더 나은 코드를 작성하는 방법에 대해 알고 싶다면 [GotW](https://herbsutter.com/gotw/) 및 [Effective Modern C++](http://shop.oreilly.com/product/0636920033707.do) 책을 참조하십시오.
