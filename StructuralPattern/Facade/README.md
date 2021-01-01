## 개요
서브 시스템, 라이브러리의 복잡한 내부구현을 모두 알고 있지 않고서도 개발자가 사용할 수 있도록 간략화된 인터페이스를 제공하도록 하는 디자인 패턴.

## 특징
* 라이브러리를 사용하는 쪽에서 코드를 조금 더 읽기 쉽게 만들어준다.
* 부 구현을 알 필요 없이, 퍼사드 인터페이스(또는 구현체)를 통해서 간단하게 사용할 수 있다.
* Decorator패턴과 차이점으로 라이브러리 혹은 API의 인터페이스가 복잡할 경우에 간단하게 변경하여 쉽게 사용하기 위한 디자인 패턴이다.

## 참고 사진
<p>
  <img src="https://user-images.githubusercontent.com/37705123/103436673-c70d2c00-4c61-11eb-899a-51a257722570.png" />
</p>

## 구현 예시(변경 전)
```kotlin
interface Package {
  fun process()
}

class PackageA : Package {
  override fun process() {
    println("패키지A 실행")
  }
}

class PackageB : Package {
  override fun process() {
    println("패키지B 실행")
  }
}

class PackageC : Package {
  override fun process() {
    println("패키지C 실행")
  }
}

fun main() {
	val packageA = PackageA()
	packageA.process()
	
	val packageB = PackageB()
	packageB.process()
	
	val packageC = PackageC()
	packageC.process()
}
```

## 구현 예시(Facade 패턴 적용)
```kotlin
interface Processor {
	fun processAll()
}

class ProcessorFacade(
  private val packageA: PackageA,
  private val packageB: PackageB,
  private val packageC: PackageC
) : Processor {
	
  override fun processAll() {
    packageA.process()
    packageB.process()
    packageC.process()
  }
}

fun main() {
  val processorFacade = ProcessorFacade(
    packageA = PackageA(),
    packageB = PackageB(),
    packageC = PackageC()
  )
	
  processorFacade.processAll()
}
```

## 실행결과
<p>
  <img src="https://user-images.githubusercontent.com/37705123/103436705-35ea8500-4c62-11eb-8f49-159b9c519404.png" width="600" />
</p>
