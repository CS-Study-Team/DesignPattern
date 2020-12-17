## 개요
- 객체의 확장성을 향상시키기 위해서 주로 사용되는 패턴으로, 객체에서 동작을 처리하는 `구현부`와 확장을 위한 `추상부`를 분리한다.
- 즉, 기능과 구현에 대해서 두 개의 별도의 클래스로 구현한다.
- (인용) 구현(Implemetation) 으로 부터 추상(Abstraction) 레이어를 분리하여, 이 둘이 서로 독립적으로 관리 될 수 있도록 한다.

## 예제 코드
```kotlin
// 확장성을 위한 인터페이스
interface MorseCode {
  fun dot()
  fun dash()
  fun space()
}

// 객체의 동작을 대신 실행시켜 주는 중간 위임자(Bridge)
class MorseCodeDelegate(private val morseCode: MorseCode)  {

  fun dot() {
    morseCode.dot()
  }
  
  fun dash() {
    morseCode.dash()
  }
  
  fun space() {
    morseCode.space()
  }
}

// 1. 점, 선, 여백으로 표기하는 모스코드 구현체
class DottedMorseCode : MorseCode {

  override fun dot() {
    println(".")
  }
  
  
  override fun dash() {
    println("-")
  }
  
  
  override fun space() {
    println(" ")
  }
}

// 2. 소리로 표기하는 모스코드 구현체
class SoundMorseCode : MorseCode {

  override fun dot() {
    println("삣!")
  }
  
  
  override fun dash() {
    println("삐~~")
  }
  
  
  override fun space() {
    println("음")
  }
}

// 3. 빛으로 표기하는 모스코드 구현체
class FlashMorseCode : MorseCode {

  override fun dot() {
    println("번쩍!")
  }
  
  
  override fun dash() {
    println("반짝!!")
  }
  
  
  override fun space() {
    println(" - ")
  }
}

fun main() {
  val morseCode1 = MorseCodeDelegate(morseCode = DottedMorseCode()) // 점,선,여백
  morseCode1.dot()
  morseCode1.dash()
  morseCode1.space()
  
  val morseCode2 = MorseCodeDelegate(morseCode = SoundMorseCode())  // 소리
  morseCode2.dot()
  morseCode2.dash()
  morseCode2.space()
  
  val morseCode3 = MorseCodeDelegate(morseCode = FlashMorseCode())  // 빛
  morseCode3.dot()
  morseCode3.dash()
  morseCode3.space()
}

```
