## 개요
클래스의 인터페이스를 사용자가 기대하는 인터페이스 형태로 적응(변환)시킵니다. 서로 일치하지 않는 인터페이스를 갖는 클래스들을 함께 동작시킵니다.

## 다른이름
래퍼(Wrapper)

## 동기
가끔은 재사용을 목표로 개발한 툴킷도 실제 재사용성을 발휘하지 못할 때가 발생한다. 즉, 응용프로그램이 요청하는 인터페이스와 툴킷에 정의된 인터페이스가 일치하지 않을 때가 있기 때문.

편집기로 예를들면, 그림 편집기는 사용자가 선, 다각형, 텍스트 등 그래픽 요소를 이용하여 그림과 다이어그램을 만들 수 있는 프로그램이다. 그림판의 중요한 개념은 그래픽 객체들인데, 이들은 모양을 편집할 수 있는 특징을 가지며, 화면에 그려질 수 있는 행동을 정의한다. 이러한 공통점을 Shape라는 추상적인 인터페이스로 정의를 해봅시다. 그 후 우리는 편집기의 각각의 그래픽 요소를 Shape의 서브클래스로 정의해야 한다. 즉, 선을 위한 LineShape, 다각형을 위한 PolygonShape 등 클래스를 개발 한다.

LineShape나 PolygonShape 클래스와 같이 아주 기본적인 그래픽 요소들은 제한적인 기능들로 이루어져 있어 구현하기 쉽다. 그러나 TextShape는 텍스트를 처리하기 위해 화면을 수정하고 버퍼를 관리하는 등 복잡한 기능을 구현해야 한다. 그래서 TextShape 클래스를 구현할때는 툴킷에서 제공되는 TextView를 재사용하는것이 바람직 하지만, 기존 구현된 툴킷은 현재 새롭게 개발된 Shape 클래스를 고려해서 개발된 것이 아니므로 바로 TextShape의 대체해 사용할 수 있는 상황이 안된다.

그렇다면 서로 일치하지 않는 인터페이스를 갖는 클래스들을 잘 통합하여 하나의 응용프로그램으로 구성하여 운영해야 할때 어떤 방법이 있을까? 다시 말해 , 기존에 만든 TextView의 인터페이스를 변경해 Shape의 인터페이스와 일치하게 만들 수 있을까? 소스가 있다면 가능할 수 있겠지만 없다면 TextView를 변경하는 것은 불가능 하다.

이미 개발된 클래스의 인터페이스를 수정할 수 없다면, Shape와 TextView 인터페이스에 맞도록 우리가 개발한 TextShape 클래스를 조정을 해야 한다. 이를 위해 우리가 할 수 있는 일은 다음과 같다.
1. Shape의 인터페이스와 TextView의 구현을 모두 상속을 받는다
2. TextView의 인스턴스를 TextShape에 포함시키고, TextView 인터페이스를 사용하여 TextShape를 구현한다.

이들 두 가지 방법은 적응자 패턴의 클래스 버전 및 객체 버전에 해당합니다

## 예제코드
```kotlin
data class Rect(
    val x: Int,
    val y: Int
)

data class Bound(
    val left: Int,
    val top: Int,
    val right: Int,
    val bottom: Int
)

interface Shape {
    val width: Int
    val height: Int

    fun getBound(): Bound
}

interface TextShape : Shape {
    fun move(x: Int, y: Int)
    fun getText(): String
    fun editText(text: String)
}

interface TextView {
    var text: String
    var rect: Rect
    fun isEmpty(): Boolean
}

class MoveableTextShape(
    private val textView: TextView,
    override val width: Int = 0,
    override val height: Int = 0
) : TextShape {
    override fun move(x: Int, y: Int) {
        textView.rect = Rect(x, y)
    }

    override fun getText(): String = textView.text

    override fun editText(text: String) {
        textView.text = text
    }

    override fun getBound(): Bound = with(textView.rect) {
        Bound(x, y, x + width, y + height)
    }

    override fun toString() = getText()
}

fun main() {
    val textView = object : TextView {
        override var text: String = "Hello"
        override var rect: Rect = Rect(0, 0)

        override fun isEmpty(): Boolean = text.isEmpty()
    }

    val textShape = MoveableTextShape(textView, 100, 150)

    println(textShape.getBound().toString()) // Bound(left=0, top=0, right=100, bottom=150)

    textShape.move(100, 100)
    println(textShape.getBound().toString()) // Bound(left=100, top=100, right=200, bottom=250)

    println(textShape) // Hello

    textShape.editText("World") // World
    println(textShape)
}
```