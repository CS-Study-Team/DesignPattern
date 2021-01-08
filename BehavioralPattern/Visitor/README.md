## 개요
연산하는 실질적인 알고리즘과 적용 대상에 대한 데이터 클래스(혹은 클래스)를 분리하는 기법

## 활용성
- 다른 인터페이스를 가진 클래스가 객체 구조에 포함되어 있으며, 구체 클래스에 따라 달라진 연산을 이들 클래스의 객체에 대해 수행하고자 할 때
- 객체 구조를 정의한 클래스는 거의 변하지 않지만, 전체 구조에 걸쳐 새로운 연산을 추가하고 싶을 때.

### 예제 코드
```kotlin

interface App {
    fun accept(exe: Executor)
}

class AndroidStudio : App {
    override fun accept(exe: Executor) {
        exe.execute(this)
    }
}

class GoLand : App {
    override fun accept(exe: Executor) {
        exe.execute(this)
    }
}

class Docker : App {
    override fun accept(exe: Executor) {
        exe.execute(this)
    }
}

interface Executor {
    fun execute(app: AndroidStudio)
    fun execute(app: GoLand)
    fun execute(app: Docker)
}

class IntelExecutor : Executor {
    override fun execute(app: AndroidStudio) {
        println("Android Studio run well on intel processor")
    }

    override fun execute(app: GoLand) {
        println("GoLand run well on intel processor")
    }

    override fun execute(app: Docker) {
        println("Docker run well on intel processor")
    }

}

class M1Executor : Executor {
    override fun execute(app: AndroidStudio) {
        println("Android Studio run failed on M1 processor")
    }

    override fun execute(app: GoLand) {
        println("GoLand run well on M1 processor")
    }

    override fun execute(app: Docker) {
        println("Docker run failed on M1 processor")
    }
}

class Rosseta2Executor : Executor {
    override fun execute(app: AndroidStudio) {
        println("Android Studio run well on Rosseta2")
    }

    override fun execute(app: GoLand) {
        println("GoLand run well on Rosseta2")
    }

    override fun execute(app: Docker) {
        println("Docker run failed on Rosseta2")
    }
}

fun main() {
    val apps = listOf(AndroidStudio(), GoLand(), Docker())

    var exe: Executor = IntelExecutor()
    apps.forEach { it.accept(exe) }
    
    exe = M1Executor()
    apps.forEach { it.accept(exe) }
    
    exe = Rosseta2Executor()
    apps.forEach { it.accept(exe) }
}
```
