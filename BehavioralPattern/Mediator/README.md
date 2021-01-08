## 개요
한 집합에 속해있는 객체의 상호작용을 캡슐화 하는 객체를 정의한다. 객체들이 직접 서로를 참조하지 않도록 하여 객체 사이의 소결합을 촉진시키며, 개발자가 객체의 상호작용을 독립적으로 다양화시킬 수 있게 만든다.

## 동기
객체지향 개발 방법론에서는 행동을 여러 객체에서 분산시켜 처리하도록 권하고 있다. 이러한 행동의 분산으로 객체 구조는 수많은 연결 관계가 객체 사이에 존재하는 형태가 된다. 게다가, 시스템은 자신의 행동을 처리하기 위해 다른 모든 객체에 대한 참조라를 관리해야 하는 최악의 상황에 직면할지도 모른다.

이때, 별도의 Mediator(중재자) 객체를 활용하면 상호작용과 관련된 행동을 하나의 객체로 모아서 이런 문제를 피해갈 수 있습니다. 중재자 객체는 객체 그룹 간의 상호작용을 제어하고 조화를 이루는 역활을 합니다. 그룹 내 객체들에 대한 포인터를 중재자 객체가 관리하기 때문에, 객체들은 다른 객체에 대한 참조자 대신 단지 해당 중재자만 알면 되므로, 객체 사이의 연결 정도가 줄어들게 된다.

class User(
    private val mediator: ChatMediator,
    private val name: String
) {
    fun send(message: String) {
        println("$name: send message [$message]")
        mediator.sendMessage(this, message)
    }

    fun receive(message: String) {
        println("$name: receive message [$message]")
    }
}

## 예제코드
```kotlin
class ChatMediator {
    private val users: MutableList<User> = arrayListOf()

    fun sendMessage(
        me: User,
        message: String
    ) = users.filter { it != me }.forEach { it.receive(message) }

    fun addUser(
        user: User
    ) = apply { users.add(user) }

    fun logout(
        user: User
    ) = apply { users.removeIf { it == user } }
}

fun main() {
    val mediator = ChatMediator()

    val kotlin = User(mediator, "kotlin")
    val go = User(mediator, "Go")

    mediator.addUser(kotlin)
        .addUser(go)
        .addUser(User(mediator, "Java"))
        .addUser(User(mediator, "C"))
        .addUser(User(mediator, "C++"))
        .addUser(User(mediator, "C#"))

    kotlin.send("Hello World")
    println()

    go.send("hi")
    println()

    mediator.logout(go)
    kotlin.send("Bye")
}
```
