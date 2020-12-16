## 개요
오직 **한 개의 클래스 인스턴스만을 갖도록 보장** 하고, 이에 대한 **전역적인 접근점을 제공** 하는 패턴

## 동기
어떤 클래스는 정확히 하나의 인스턴스만을 갖도록 하는 것이 좋을 수 있다. 시스템에 많은 프린터가 있어도 프린터 스풀은 오직 하나여야 하고, 회사에서는 한 가지의 회계 시스템으로 운영이 되어야 한다.

그렇다면 어떻게 해야 클래스의 인스턴스를 하나만 만들고, 쉽게 접근할 수 있을까? 전역변수를 이용해서 객체에 접근하도록 하면 가능은 하다.

그러나 더 좋은 방법은 클래스 자체적으로 유일한 인스턴스로 접근할 수 있는 방법을 제공하는것이 좋습니다. 이 클래스는 **또 다른 인스턴스가 생성되지 않도록** 할 수 있고(새로운 클래스 생성에 대한 요청을 가로채는 방법), **클래스 자신이 그 인스턴스에 대해 접근 방법을 제공** 할 수 있다. 이를 가르켜 **단일체패턴(Singleton Pattern)** 이라고 한다.

## 활용성
* 클래스의 인스턴스가 오직 하나여야 함을 보장하고, 잘 정의된 접근점(Access Point) 으로 모든 사용자가 접근할 수 있도록 해야 할 때
* 유일한 인스턴스가 서브클래싱으로 확장되어야 하며, 사용자는 코드의 수정없이 확장된 서브클래스의 인스턴스를 사용할 수 있어야 할 때


## 구현
1. 인스턴스가 유일해야 함을 보장합니다.
2. Singleton 클래스를 서브클래싱합니다.

```kotlin
object ObjectPrinter {
    fun printWithAddress(text: String) = println("$text $this")
}

class ClassPrinter private constructor() {
    fun printWithAddress(text: String) = println("$text $this")

    companion object {
        @Volatile
        private var instance: ClassPrinter? = null

        fun getInstance(): ClassPrinter = instance ?: synchronized(this) {
            instance ?: ClassPrinter().also {
                instance = it
            }
        }
    }
}


fun main() {
    ObjectPrinter.printWithAddress("Hello") // Hello ObjectPrinter@29453f44
    ObjectPrinter.printWithAddress("World") // World ObjectPrinter@29453f44

    ClassPrinter.getInstance().printWithAddress("Hello") // Hello ClassPrinter@5cad8086
    ClassPrinter.getInstance().printWithAddress("World") // World ClassPrinter@5cad8086
}
```

## 결과
1. 유일하게 존재하는 인스턴스로의 접근을 통제합니다.
    * Singleton 클래스 자체가 인스턴스를 캡슐화하기 때문
2. 이름 공간(name space)을 좁힙니다.
    전역 변수를 정의하여 발생하는 디버깅의 어려움 등 문제를 방지
3. 연산 및 표현의 정제를 허용합니다.
    * Singleton 클래스는 상속될 수 있기 때문에, 이 상속된 서브클래스를 통해서 새로운 인스턴스를 만들 수 있습니다. 또한 이 패턴을 사용하면, 런타임에 필요한 클래스의 인스턴스를 써서 응용프로그램을 구성이 가능함.
4. 인스턴스의 갯수를 변경하기가 자유롭습니다.
    * 마음이 바뀌어서 Singleton 클래스의 인스턴스가 하나 이상 존재할 수 있도록 변경해야 할 때도 있는데, 여러개의 인스턴스를 생성해서 각각의 인스턴스로 접근할 수 있도록 연산의 구현을 바꾸면 되기 때문입니다.
5. 클래스 연산을 사용하는 것보다 훨씬 유연한 방법입니다.