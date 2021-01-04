## 개요
이벤트를 발생하는 객체와 이를 받아 처리하는 객체들 간의 결합도를 없애고, **하나의 이벤트**를 **하나의 객체**가 처리하는 것이 아닌 **여러 객체**에게 그 **이벤트 처리 기회**를 주려는 것.

## 활용성
- 하나 이상의 객체가 요청을 처리해야 하고, 그 요청 처리자 중 어떤 것이 선행자인지 모를 때
- 이벤트를 받은 객체를 명시하지 않고 여러 객체 중 하나이상 에게 처리를 요청하고 싶을 때
- 처리 하고자 하는 객체가 동적으로 정의가 되어야 할때

### 예제 코드
```kotlin
enum class Rank {
    이등병, 일등병, 상병, 병장;
}

enum class 행보관스킬 {
    진지공사, 생활관청소, 병장찾기;
}

abstract class 병사(val rank: Rank) {
    private var next: 병사? = null
    fun relationNext(next: 병사): 병사 {
        this.next = next
        return next
    }

    fun work(skill: 행보관스킬) {
        when(skill) {
            행보관스킬.진지공사 -> {
                if (rank == Rank.이등병 || rank == Rank.일등병 || rank == Rank.상병) {
                    action()
                }
            }
            행보관스킬.생활관청소 -> {
                if (rank == Rank.이등병 || rank == Rank.일등병) {
                    action()
                }
            }
            행보관스킬.병장찾기 -> {
                if (rank == Rank.병장) {
                    action()
                }
            }
        }

        next?.work(skill)
    }

    abstract fun action()
}

class 신병 : 병사(Rank.이등병) {
    override fun action() {
        println("어리버리 하게 열심히 한다.")
    }
}

class S급 : 병사(Rank.이등병) {
    override fun action() {
        println("눈치 있게 열심히 한다.")
    }
}

class 열심히하는병사 : 병사(Rank.일등병) {
    override fun action() {
        println("일병은 역시 일하는 병ㅅ..ㅏ 여서 아주 열심히 한다.")
    }
}

class 실세 : 병사(Rank.상병) {
    override fun action() {
        println("실세 답게 할건 하고 뺄건 빼면서 일한다.")
    }
}

class 곧전역자 : 병사(Rank.병장) {
    override fun action() {
        println("모든지 귀찮아 한다.")
    }
}

fun main() {
    val 분대장 = 실세()
    var 군인: 병사 = 분대장.relationNext(곧전역자())
    군인 = 군인.relationNext(열심히하는병사())
    군인 = 군인.relationNext(S급())
    군인.relationNext(신병())

    분대장.work(행보관스킬.생활관청소)
    분대장.work(행보관스킬.진지공사)
    분대장.work(행보관스킬.병장찾기)
}
```
