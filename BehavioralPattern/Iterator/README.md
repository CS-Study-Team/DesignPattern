## 개요
**반복자(Iterator)** 는 객체 지향적 프로그래밍에서 배열이나 그와 유사한 자료 구조의 내부의 요소를 순회(traversing)하는 객체이다.

## 다른이름
Cursor

## 동기
List 등 집합 객체(aggregate object)들은 이 내부 표현 구조를 노출하지 않고도 자신의 원소를 접근할 수 있는 방법을 제공하는게 좋다. 추가적으로, 이미 정의된 방법과 다른 방식으로 원소들을 순회하고자 할 수도 있다. 하지만 순회 방법이 바뀌었다고 List Class의 Interface를 변경하고 싶지는 않을것이다.

이런 문제를 해결하기 위해 만들어진 것이 **반복자** 패턴 이다. 이 패턴의 핵심은 List 객체에 접근해서 새로운 내용은 삽입, 삭제 혹은 순회하는 내용을 반복자 객체에 정의하는 것. 즉 반복자 객체를 나타내는 Iterator Class는 원소들에 접근하는데 필요한 interface를 제공합니다. 즉 반복자 객체는 현재 원소가 무엇인지 관리하고, 이미 방문한 원소들이 무엇인지 알고 있다.

## 활용성
반복자 패턴은 이런 목적으로 사용 됩니다.

* 객체 내부 표현 방식을 모르고도 집합 객체의 각 원소들에 접근하고 싶을 때
* 집합 객체를 순회하는 다양한 방법을 지원하고 싶을 때
* 서로 다른 객체 구조에 대해서도 동일한 방법으로 순회하고 싶을 때

## 예제코드
```kotlin
class LinkedList<T> {
    private data class Node<T>(val nodeValue: T, var next: Node<T>? = null)
    private var head: Node<T>? = null

    fun insert(item: T) {
        if (head == null) head = Node(item)

        var cursor = head

        while (cursor?.next != null) {
            cursor = cursor.next
        }

        cursor?.next = Node(item)
    }

    fun forEach(action: (T) -> Unit) {
        var cursor = head
        while (cursor != null) {
            action(cursor.nodeValue)
            cursor = cursor.next
        }
    }
}

fun main() {
    val linkedList = LinkedList<Int>()
    linkedList.insert(1)
    linkedList.insert(2)
    linkedList.insert(3)
    linkedList.insert(4)
    linkedList.insert(5)

    linkedList.forEach {
        println(it)
    }
}
```