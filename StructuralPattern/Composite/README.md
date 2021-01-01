
## 정의
- 단일 객체, 객체들의 집합에 관계없이 모두 같은 방법으로 다루고자 할때 사용할 수 있는 패턴이다.
- 개별적인 객체들과, 객체들의 집합 간의 처리방법에 차이가 없을 경우에 유용하게 사용할 수 있다.
- Composite 패턴을 구성하는 요소로는 `Component`, `Composite`, `Leaf`, `Client`가 있다.

## 활용
- Composite패턴을 활용한 사례로는 `파일시스템`, `쇼핑몰 카테고리`, `그래픽 작업시 사용하는 도형 그룹` 등이 있다.
- 이들의 공통점으로는 단일 객체와 복합 객체를 구별없이 취급한다는 `전체-부분 관계`를 가지는 형태라는 것이다.
- 동일한 `인터페이스` 혹은 `추상 클래스`를 상속하여 구현할 수 있는데, 아래 예제에서는 `인터페이스`를 사용하여 구현한 예시를 보여주도록 할것이다.

## 예제코드(파일 시스템)
```kotlin
// 파일 및 디렉토리에서 상속받을 인터페이스
interface Data {
  val name: String
  fun doOnRemove()
}

// 삽입, 삭제가 가능한 형태를 의미하는 추상 클래스
abstract class Changeable {
  private val dataList = arrayListOf<Data>()
    
  fun add(data: Data) {
    dataList.add(data)
  }
    
  fun remove(data: Data) {
    data.doOnRemove()
    dataList.remove(data)
  }
  
  fun clear() {
    dataList.forEach { it.doOnRemove() }
    dataList.clear()
  }
    
  fun count() = dataList.size
}

// 파일
class File(private val fileName: String) : Data {
  override val name: String
    get() = fileName
    
  override fun doOnRemove() {
    println("$name is deleted")
  }
}

// 디렉토리
class Directory(private val fileName: String) : Changeable, Data {
  override val name: String
    get() = fileName
    
  override fun doOnRemove() {
    this.clear()
    println("$name is deleted")
  }
}

// 파일 시스템
class FileSystem : Changeable

fun main() {
  val directory = Directory("개발 스터디")
  directory.add(File("디자인패턴"))
  directory.add(File("운영체제"))
  directory.add(File("안드로이드"))
  
  val file1 = File("샘플코드")
  val file2 = File("영화")
  val file3 = File("개발 아티클")
  
  // 파일 시스템 생성
  val fileSystem = FileSystem()
  fileSystem.add(directory)
  fileSystem.add(file1)
  fileSystem.add(file2)
  fileSystem.add(file3)
  
  // 모든 파일 삭제
  fileSystem.clear()
}
```
