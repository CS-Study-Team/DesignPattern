## 개요
상세화된 서브클래스를 정의하지 않고도 서로 관련성이 있거나 독립적인 여러 객체를 생성하기 위한 인터페이스를 제공하는 패턴

## 활용성
- 객체가 생성되거나 구성되는 방식과 무관하게 시스템을 독립적으로 만들고자할때
- 여러 객체 중 하나를 선택해서 시스템을 설정해야 하고 한번 구성한 제품을 다른 것으로 대체할 수 있게끔 유연성을 주고 싶을 때
- 관련 객체들이 함께 사용되도록 설계되었고, 관련 객체 구성에 대한 제약이 외부에도 지켜지도록 하고 싶을 때 / **
- 관련 설계에 대한 클래스 라이브러리를 제공하고, 개발자들이 패턴 및 설계 구현이 아닌 해당 설계에 인터페이스를 노출시키고 싶을 때 / **

### 예제 코드
```kotlin
abstract class Material

open class Battery : Material() {
    override fun toString(): String {
        return "bulk battery"
    }
}

open class Hardware : Material() {
    var powerSource: Battery? = null
}

open class Display : Hardware() {
    override fun toString(): String {
        return if (powerSource == null) "none power source" else "samsung display"
    }
}

open class NetworkChip : Hardware() {
    override fun toString(): String {
        return if (powerSource == null) "none power source" else "samsung display"
    }
}

class GalaxyPhone(var modelName: String) {
    var display: Display? = null
    var battery: Battery? = null
    var networkChip: NetworkChip? = null

    override fun toString(): String {
        return "model($modelName), display($display), battery($battery), networkChip($networkChip)"
    }
}

abstract class GalaxyFactory {
    open fun createPhone(): GalaxyPhone {
        return GalaxyPhone("S10")
    }
    open fun createNetworkChip(): NetworkChip {
        return NetworkChip()
    }
    open fun createDisplay(): Display {
        return Display()
    }
    open fun createBattery(): Battery {
        return Battery()
    }
}

class S10Factory : GalaxyFactory()

class _5GNetworkChip : NetworkChip() {
    override fun toString(): String {
        return "5G chip"
    }
}

class S10_5GFactory : GalaxyFactory() {
    override fun createPhone(): GalaxyPhone {
        return GalaxyPhone("S10 5G")
    }

    override fun createNetworkChip(): NetworkChip {
        return _5GNetworkChip()
    }
}

class Samsung {
    fun createPhone(factory: GalaxyFactory): GalaxyPhone {
        return factory.createPhone().apply {
            battery = factory.createBattery()
            display = factory.createDisplay().apply {
                powerSource = battery
            }
            networkChip = factory.createNetworkChip().apply {
                powerSource = battery
            }
        }
    }
}


fun main() {
    val samsung = Samsung()
    val s10Factory = S10Factory()
    val s10_5GFactory = S10_5GFactory()

    //가상의 특정 일자 1분기
    var phone = samsung.createPhone(s10Factory)
    println(phone)

    //가상의 특정 일자 2분기
    phone = samsung.createPhone(s10_5GFactory)
    println(phone)

    //가상의 특정 일자 3분기
    phone = samsung.createPhone(s10_5GFactory)
    println(phone)

    //가상의 특정 일자 4분기
    phone = samsung.createPhone(s10Factory)
    println(phone)
}
```
