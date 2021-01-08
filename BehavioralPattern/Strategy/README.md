# 스트레티지(전략) 패턴

## 정의
알고리즘 군(familiy)을 정의하고 각각을 캡슐화(encapsulate)하여 교환해서 사용할 수 있도록 한다. 클라이언트의 코드는 변경할 필요없이 알고리즘을 변경할 수 있다.

## 예시
컴퓨터 본체 혹은 노트북에 모니터를 연결해서 사용하고 싶다. 보통 본체의 영상 입력 단자(vga, dvi, hdmi, c type 멀티포트, usb 3.0 등의 포맷)에서 모니터 출력 단자(역시 vga, dvi, hdmi, dp 등의 형식)로 연결하게 된다.

선택할 수 있는 여러 종류의 컨버터가 있다. 
- HDMI to HDMI
- HDMI to VGA
- USB 3.0 type to HDMI
- c type to HDMI

컨버터를 총칭하는 추상 클래스를 구현한다. 
```kotlin
abstract class Converter {
  abstract fun input()
  abstract fun output()
}
```

case by case로 Converter class를 implements 하여 상속하면 코드 중복을 야기할 수 있다. Converver class의 구현체인 HDMIToHDMIConverter와 CTypeToHDMIConverter는 같은 계층(hirarchy)에 위치하고 있기 때문에 
```kotlin
class HDMIToHDMIConverter : Converter {
  override fun input(){
    println("HDMI 인풋 신호 알고리즘")
  }

  override fun output(){
    println("HDMI 아웃풋 신호 알고리즘")
  }
}

class CTypeToHDMIConverter : Converter {
  override fun input(){
    println("C Type 인풋 신호 알고리즘")
  }

  override fun output(){
    println("HDMI 아웃풋 신호 알고리즘")
  }
}
```

먼저 변경될 소지가 있는 기능을 분리해보자. 일단 input, output의 기능은 공통적으로 갖고 있다. 그러나 input, output의 내부 구현은 달라질 수 있다. `input`과 `output` 메소드를 분리하여 인터페이스화 한다.
```kotlin
interface IInput {
  fun input()
}

interface IOutput {
  fun output()
}
```

물론 공통 Interface로 묶어서 한번에 Input, Output을 통칭하는 코드를 작성할 수도 있겠으나 이해를 쉽게 하기 위해 `IInput`, `IOutput`으로 인터페이스를 분리하는 것으로 하겠다. (코틀린이나 자바 8 이상에서는 Lambda로 구현해도 된다.)
```kotlin
interface ISignal {
  // input, output 작업을 통칭
  fun invoke()
}
```

`IInput`을 상속한 구현체들은 **알고리즘 군(family)**을 이루게 된다.
```kotlin
class HDMIInput : IInput {
  override fun input(){
    println("HDMI 인풋 신호 알고리즘")
  }
}

class CTypeInput : IInput {
  override fun input(){
    println("C type 인풋 신호 알고리즘")
  }
}

class USB3.0Input : IInput {
  override fun input(){
    println("USB 3.0 인풋 신호 알고리즘")
  }
}

class VGAInput : IInput {
  override fun input(){
    println("VGA 인풋 신호 알고리즘")
  }
}
```

`IOutput`을 상속한 구현체들도 역시 **알고리즘 군(family)**을 이루게 된다.
코드는 비슷할 것이므로 생략하기로 하겠다.

이렇게 내부 구현이 상황에 따라 달라지는 부분만 추려서 인터페이스화 시키면, Converter 추상 클래스 전체를 상속받지 않아도 된다. (composition over inheritance 라고 한다.) 즉 상단에 만들어 놓았던 HDMIToHDMIConverter 클래스와 CTypeToHDMIConverter 클래스는 더 이상 필요없게 된다. 

기존 abstract class 였던 Converter클래스의 abstract 지시어를 삭제한 후, 생성자에 IInput, IOutput 인터페이스 구현체를 DI(Dependency Injection) 해주는 방식으로 코드를 다시 작성해보자.
```kotlin
class Converter(private val iInput: IInput, private val iOutput: IOutput) {
  fun input(){
    // 캡슐화 --> 무슨 IInput 구현체가 들어올 지 알 수 없다.
    iInput.input()
  }

  fun output(){
    // 캡슐화 --> 무슨 IOutput 구현체가 들어올 지 알 수 없다.
    iOutput.output()
  }
}
```

## 정리
- Client 코드의 "내부 구현이 달라지는 알고리즘"은 캡슐화되어 향후 기능이 추가되어도 변경할 필요가 없다.
- 전략 패턴에서는 Composition(조합)이 Inheritance(상속)보다 유리하다. (알고리즘의 재사용 측면, SOLID OCP - 개방 폐쇄 원칙에 부합)
