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

팩토리 메서드 패턴을 구현하는 방법은 크게 두 가지가 있다. 
- 앞선 예시처럼 Creator(TextEditor)를 추상 클래스로 정의하고, 정의한 팩토리 메소드에 대한 구현을 제공하지 않는 경우. 정의한 팩토리 메소드 역시 추상 메소드로 지정한다.
- Creator(TextEditor)를 구체 클래스로 정의하고, 팩토리 메소드의 디폴트 구현을 제공하는 경우
  - 팩토리 메서드를 매개변수화 할 수도 있다.

```kotlin
// type: DocumentType 이라는 Enum 변수를 매개변수로 넘기면 팩토리 메서드가 매개변수를 받아서 어떤 종류의 제품을 생성할지 식별할 수 있다. 여기서는 제공 문서를 Java, Kotlin, Cpp를 기본값으로 정의하였는데, TextEditor의 서브클래스에서 이를 오버라이드하여 문서 생성에 대한 로직을 더 추가할 수도 있을 것이다.
class TextEditor {
  protected fun createDocument(type: DocumentType): Document {
    when(type){
      DocumentType.JAVA -> JavaDocument()
      DocumentType.KOTLIN -> KotlinDocument()
      DocumentType.CPP -> CppDocument()
    }
  }
}
```

팩토리 메서드를 쓰면 Document 클래스를 추가하려 할 때마다 서브클래싱을 해야한다. MSWordTextEditor는 .docx 포맷의 문서를 반환 받는다고 가정하자. DocxDocument() 클래스를 추가하려면 이를 반환받으려는 TextEditor의 서브 클래스인 MSWordTextEditor를 만들어야 한다. 이 과정은 반복적이기 때문에, 템플릿을 사용하여 피해야 한다.
```kotlin
abstract class TextEditor {
  protected abstract fun createDocument(): Document
 
  fun create(): Document {
    return createDocument()
  }
}

// TextEditor을 상속한다.
class MSWordTextEditor: TextEditor {
  override fun createDocument(): Document {
    return DocxDocument()
  }
}
```
 
### 정리
---
팩토리 메소드 패턴을 이용할 경우
- 응용 프로그램의 프레임워크 레벨에서 구체 클래스에 대한 정보를 은닉할 수 있다.
- 동시에 프레임워크가 추상 클래스를 인스턴스화하지 못한다는 단점도 해결한다.
