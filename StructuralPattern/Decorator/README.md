## 개요
상속의 단점을 보완하면서 기능을 동적으로 추가할 수 있도록 해주는 디자인 패턴

## 상속의 한계
* **상속**은 클래스를 확장하는 대표적인 방법이지만, 상위 클래스와 하위클래스 간에 강한 결합 관계가 생성된다는 단점이 있다. 클래스 간 강력한 결합은 객체의 유연한 확장을 어렵게 한다. 또한 새로운 기능을 추가할 때 마다 메서드를 오버라이드 해야 한다는 점도 단점으로 꼽을 수 있다.
* **상속**은 정적 방식으로 기능ㄹ 확장 하기때문에, 빌드 후 실행이 된 다음에는 수정이 불가능하다.

## 활용 상황
* 객체가 상황에 따라 동적으로 추가되거나 삭제되어야 하는 상황
* 예를 들면, 안드로이드에서 버튼 클릭 또는 스위치 토클 조작으로 인해서 기능이 런타임에 변경되어야 하는 상황에서 유용하게 사용할 수 있다.

## 구현 예시
* 크리스마스 트리
```kotlin
// 크리스마스 트리를 정의하는 인터페이스
interface ChristmasTree {
    fun decorate(): String
}

// 크리스마스 트리 구현체
class ChristmasTree2020(
    private val title: String
) : ChristmasTree {
    private val decorators = arrayListOf<TreeDecorator>()
    
    override fun decorate(): String {
        val decorations = decorators.joinToString(separator = " ") { it.decorate() }
        return "$title $decorations"
    }
    
    fun addDecorator(decorator: TreeDecorator) {
        decorators.add(decorator)
    }
}
```
* 데코레이터
```kotlin
// 데코레이터로 사용할 추상클래스
// 생성자에서 Tree 구현체를 받은 후에, decorate 함수를 실행시킨다.
abstract class TreeDecorator(
    private val tree: ChristmasTree
) : ChristmasTree {
    
    override fun decorate(): String = tree.decorate()
}

class Lights(tree: ChristmasTree) : TreeDecorator(tree = tree) {
    override fun decorate(): String = "${super.decorate()} ${addLights()}"
    private fun addLights() = "with Lights"
}

class Flowers(tree: ChristmasTree) : TreeDecorator(tree = tree) {
    override fun decorate(): String = "${super.decorate()} ${addFlowers()}"
    private fun addFlowers() = "with Flowers"
}
```

* 데코레이터를 통해서 객체에 기능 추가
```kotlin
fun main() {
    val christmasTree2020 = ChristmasTree2020(title = "크리스마스 트리 2020")
        .apply {
            addDecorator(Lights())
            addDecorator(Flowers())
        }
    
    println(christmasTree2020.decorate())
}
```
