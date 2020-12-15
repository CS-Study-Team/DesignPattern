# 팩토리 메서드 패턴
---
 
VSCode는 사용자에게 다양한 종류(.py, .java, .kt 등)의 문서를 제공하는 응용프로그램이다. 텍스트 에디터의 기능을 하는 응용프로그램은 VSCode 외에도 NotePad++, SublimeText 등 여러개가 있다. 
- 텍스트 에디터 기능을 하는 응용프로그램 프레임워크를 TextEditor 추상 클래스로 정의하자.
- 응용프로그램이 지원하는 다양한 문서 포맷을 인터페이스 Document 클래스로 정의하자.
 
```kotlin
abstract class TextEditor {
  protected abstract fun createDocument(type: DocumentType): Document // 팩토리 메소드
 
  fun create(type: DocumentType): Document {
    return createDocument(type)
  }
}
 
interface Document {
  fun open()
  fun close()
  fun save()
  fun revert()
}
```
추상 클래스인 TextEditor는 VSCode, NotePad++, SublimeText, 심지어 MS Word 등의 대표격이다. 어떤 종류의 응용 프로그램을 선택하느냐에 따라 그에 종속되는 문서의 서브클래스가 달라진다. 예를 들어 MS Word를 선택해서 구현하게 되면, 이에 종속되는 문서가 .docx 포맷이 될 수는 있지만, .cpp 포맷이 되지는 못한다. 따라서 TextEditor 클래스는 언제 Document의 인스턴스를 만들어야 하는지만 알고 있을 뿐(`create()` 메소드), 어떤 종류의 문서를 생성해야 하는지 사전에 알지 못한다. 
 
**프레임워크는 추상 클래스 밖에 모르기 때문에, 추상 클래스의 인스턴스화 작업을 할 수 없게 된다.** 팩토리 메소드 패턴이 이 문제를 해결할 수 있다.
 
위 코드에서 `createDocument()`로 정의된 메소드는 팩토리 메소드라 칭한다. `createDocument()`에 넘긴 매개변수 `type: DocumentType`에 근거하여 어떤 종류의 Document를 만들 것인가를 알고 있기 때문이다. 팩토리 메소드 `createDocument()`는 Document의 서브클래스 중 어느 것을 생성해야 하는지에 대한 정보를 은닉하여 프레임워크 단에서 알 수 없도록 한다.
 
TextEditor 클래스의 서브클래스가 VSCode라면 VSCode는 `createDocument()`를 재정의하여, VSCode가 지원하는 포맷의 Document 서브클래스를 반환할 것이다. 
 
```kotlin
abstract class TextEditor {
  protected abstract fun createDocument(type: DocumentType): Document
 
  fun create(type: DocumentType): Document {
    return createDocument(type)
  }
}
 
interface Document {
  fun open()
  fun close()
  fun save()
  fun revert()
}
```
