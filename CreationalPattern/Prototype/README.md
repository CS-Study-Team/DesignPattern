## 개요
기준이 되는 인스턴스를 사용하여 추후 생성할 객체를 명시하고 해당 인스턴스를 복사해서 새로운 객체를 생성합니다.

## 활용성
- 인스턴스화할 클래스를 런타임에 지정할 때(이를테면, 동적 로딩)
- 초기에 관련 클래스와 병렬적으로 만드는 초기 팩토리 클래스를 피하고 싶을 때

### 예제코드
```kotlin
abstract class Material {
    abstract fun clone(): Material
}

open class Battery(private val amp: String) : Material() {

    constructor(src: Battery): this(src.amp)

    override fun toString(): String {
        return amp
    }

    override fun clone(): Material {
        return Battery(this)
    }
}


open class Display(private val inch: String) : Material() {
    constructor(src: Display): this(src.inch)
    override fun toString(): String {
        return "display $inch"
    }
    override fun clone(): Material {
        return Display(this)
    }
}

open class NetworkChip(private val generation: String) : Material() {
    constructor(src: NetworkChip): this(src.generation)
    override fun toString(): String {
        return generation
    }

    override fun clone(): Material {
        return NetworkChip(this)
    }
}

class GalaxyPhone(private val modelName: String,
                  private val networkChip: NetworkChip,
                  private val display: Display,
                  private val battery: Battery) {

    override fun toString(): String {
        return "model($modelName), display($display), battery($battery), networkChip($networkChip)"
    }
}

class GalaxyPrototypeFactory(
        private val prototypeNetwork: NetworkChip,
        private val prototypeDisplay: Display,
        private val prototypeBattery: Battery
) {
    fun createPhone(networkChip: NetworkChip,
                         display: Display,
                         battery: Battery): GalaxyPhone {
        return GalaxyPhone("S10", networkChip, display, battery)
    }
    fun createNetworkChip(): NetworkChip {
        return prototypeNetwork.clone() as NetworkChip
    }
    fun createDisplay(): Display {
        return prototypeDisplay.clone() as Display
    }
    fun createBattery(): Battery {
        return prototypeBattery.clone() as Battery
    }
}

class Samsung {
    fun createPhone(factory: GalaxyPrototypeFactory): GalaxyPhone {
        return factory.createPhone(
                factory.createNetworkChip(),
                factory.createDisplay(),
                factory.createBattery()
        )
    }
}


fun main() {
    val samsung = Samsung()
    val s10Battery = Battery("3400mAh")
    val s10Factory = GalaxyPrototypeFactory(
            NetworkChip("LTE"),
            Display("6.1"),
            s10Battery
    )

    val s10_5GBattery = Battery("4500mAh")
    val s10_5GFactory = GalaxyPrototypeFactory(
            NetworkChip("5G"),
            Display("6.7"),
            s10_5GBattery
    )

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
