# 템플릿 메소드 패턴

## 정의
- 상위 클래스에 템플릿의 역할을 하는 **템플릿 메소드**가 정의된다.
  - 이 메소드 내부에서 추상 메소드들을 호출한다.
  - 템플릿 메소드 내부를 보면, 추상 메소드들이 어떤 순서로 호출되는지는 알 수 있으나, 구체적인 처리가 어떻게 이루어지는지는 모른다.
- 하위 클래스는 추상 메소드를 구현한다.
  - 하위 클래스에서 어떤 구현을 하더라도, 처리의 흐름은 상위 클래스가 정의한대로 이루어진다.
  
## 예시

```kotlin
abstract class HumanLifecycle {
  // 로직을 공통화 (template method)
    fun humanLife() {
        println("---출생---")
        출생()

        println("---유년시절---")
        유년시절()

        println("---소년시절---")
        소년시절()

        println("---청년시절---")
        청년시절()

        println("---장년시절---")
        장년시절()
        
        println("---사망---")
        사망()
    }

    protected open fun 출생() {
        println("출생 미상")
    }
    protected abstract fun 유년시절()
    protected abstract fun 소년시절()
    protected abstract fun 청년시절()
    protected abstract fun 장년시절()
    protected open fun 사망() {
        println("사망 미상")
    }
}
```

```kotlin
class SilverSpoonLifecycle: HumanLifecycle() {
    override fun 출생() {
        println("강남 타워팰리스에서 출생")
    }

    override fun 유년시절() {
        println("명문 사립 영운초등학교에 재학")
    }

    override fun 소년시절() {
        println("미국 샌프란시스코로 유학")
    }

    override fun 청년시절() {
        println("UC 버클리 재학")
    }

    override fun 장년시절() {
        println("미국 S&P 100대 기업 수억대 연봉 재직")
    }

    override fun 사망() {
        println("전용 요트 난파로 사망")
    }
}

class PlasticSpoonLifecycle : HumanLifecycle(){
    override fun 출생() {
        super.출생()
        println("어머니 가출")
    }
    
    override fun 유년시절() {
        println("아버지 사망")
        println("할머니 손에서 자람")
    }

    override fun 소년시절() {
        println("임대 아파트에 운 좋게 들어갔으나 학우들에게 휴먼시아 거지로 놀림 받음")
    }

    override fun 청년시절() {
        println("일용직으로 생계 유지")
    }

    override fun 장년시절() {
        println("배달업으로 생계 유지")
    }

}
```

```kotlin
fun main() {
    val silverSpoonLifecycle = SilverSpoonLifecycle().humanLife()
    println("")
    val plasticSpoonLifecycle = PlasticSpoonLifecycle().humanLife()
}
```

```
---출생---
강남 타워팰리스에서 출생
---유년시절---
명문 사립 영운초등학교에 재학
---소년시절---
미국 샌프란시스코로 유학
---청년시절---
UC 버클리 재학
---장년시절---
미국 S&P 100대 기업 수억대 연봉 재직
---사망---
전용 요트 난파로 사망

---출생---
출생 미상
어머니 가출
---유년시절---
아버지 사망
할머니 손에서 자람
---소년시절---
임대 아파트에 운 좋게 들어갔으나 학우들에게 휴먼시아 거지로 놀림 받음
---청년시절---
일용직으로 생계 유지
---장년시절---
배달업으로 생계 유지
---사망---
사망 미상
```

## 실제 사용 예
명시된 클래스의 non-abstract 메소드
- `java.io.InputStream`
- `java.io.OutputStream`
- `java.io.Reader`
- `java.io.Writer`
- `java.util.AbstractList`
- `java.util.AbstractSet`
- `java.util.AbstractMap`
- `javax.suvlet.http.HttpServlet`의 `doXXX()` 메소드
