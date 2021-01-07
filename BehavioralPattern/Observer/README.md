## 의도
옵서버 패턴(observer pattern)은 객체의 상태 변화를 관찰하는 관찰자들, 즉 옵저버들의 목록을 객체에 등록하여 상태 변화가 있을 때마다 메서드 등을 통해 객체가 직접 목록의 각 옵저버에게 통지하도록 하는 디자인 패턴이다. 주로 분산 이벤트 핸들링 시스템을 구현하는 데 사용된다.

## 다른 이름
종속자(Dependent), 게시-구독(Publish-Subscribe)

## 동기
어떤 하나의 시스템을 서로 연동되는 클래스 집합으로 분할했을 때 발생하는 공통적인 부작용은 관련된 객체 간에 일관성을 유지하도록 해야 한다는 것이다. 그렇다고 일관성 관리를 위해 객체 간의 결합도를 높이게 되면 각 클래스의 재사용성이 떨어지기 때문에 좋지 않은 방법이다.

스프레드시트와 바 차트는 데이터 객체에 종속적이기 때문에 그 데이터에 일어난 변경을 통보받아야 한다. 그러나 반드시 이 예처럼 종속적인 객체의 개수가 두개로 제한되는 것이 아니다. 서로 다른 다수의 사용자 인터페이스가 하나의 데이터에 종속될 수 있다.

감시자 패턴은 이런 관련성을 관리하는 패턴입니다. 이 패턴에서 중요한 객체는 주체(Subject)와 감시자(Observer)입니다. 주체는 독립된 여러 개의 감시가 있을 수 있기때문에 모든 감시자는 주체의 상태 변화가 있을 때마다 이 변화를 통보 받는다.

## 예제코드
```kotlin
interface Observer {
    fun update(value: Int)
}

interface Subject {
    fun registerObserver(observer: Observer)
    fun removeObserver(observer: Observer)
    fun notifyObserver()
}

class ConcreteSubject : Subject {
    private val observers = mutableListOf<Observer>()
    var value = 0
        set(value) {
            field = value
            notifyObserver()
        }

    override fun registerObserver(observer: Observer) {
        observers.add(observer)
    }

    override fun removeObserver(observer: Observer) {
        observers.remove(observer)
    }

    override fun notifyObserver() {
        observers.forEach { it.update(value) }
    }
}

class A(subject: ConcreteSubject) : Observer {

    init {
        subject.registerObserver(this)
    }

    override fun update(value: Int) {
        println("A Class update value [$value]")
    }
}

class B(subject: ConcreteSubject) : Observer {

    init {
        subject.registerObserver(this)
    }

    override fun update(value: Int) {
        println("B Class update value [$value]")
    }
}

class C(subject: ConcreteSubject) : Observer {

    init {
        subject.registerObserver(this)
    }

    override fun update(value: Int) {
        println("C Class update value [$value]")
    }
}

fun main() {
    val concreteSubject = ConcreteSubject()

    A(concreteSubject)
    B(concreteSubject)
    C(concreteSubject)

    concreteSubject.value = 10
    println()
    concreteSubject.value = 20
    println()
    concreteSubject.value = 30
}
```