## 개요
요청을 객체의 형태로 캡슐화하여 사용자가 보낸 요청을 나중에 이용할 수 있도록 매서드 이름, 매개변수 등 요청에 필요한 정보를 저장 또는 로깅, 취소할 수 있게 하는 패턴이다.

## 활용성
- 수행할 동작을 객체로 매개변수화하고자 할 때
- 서로 다른 시간에 요청을 명시하고, 저장하며, 실행하고 싶을 때
- 실행 취소 기능을 지원하고 싶을 때
- 시스템이 고장 났을 때 재적용이 가능하도록 변경 과정에 대한 로깅을 지원하여 로깅된 명령어를 다시 재실행 하고자 할 때

### 예제 코드
```kotlin
abstract class Command {
    abstract fun execute()
}

class Pen : Command() {
    override fun execute() {
        println("draw something with pen")
    }
}

class Shape(val shape: String) : Command() {
    override fun execute() {
        println("draw $shape")
    }
}

class PaintBucket : Command() {
    override fun execute() {
        println("fill inside")
    }
}

class Eraser : Command() {
    override fun execute() {
        println("erase")
    }
}

class Canvas {
    private val commands = Stack<Command>()
    private val canceledCommands = Stack<Command>()

    fun draw(cmd: Command) {
        commands.push(cmd)
        if (canceledCommands.isNotEmpty()) canceledCommands.clear()
    }

    fun undo() {
        if (commands.isNotEmpty()) canceledCommands.push(commands.pop())
    }

    fun redo() {
        if (canceledCommands.isNotEmpty()) commands.push(canceledCommands.pop())
    }

    fun show() {
        commands.forEach { it.execute() }
    }
}

fun main() {
    val canvas = Canvas()

    //tools
    val pen = Pen()
    val paintBucket = PaintBucket()
    val eraser = Eraser()
    val rectangle = Shape("rectangle")
    val triangle = Shape("triangle")

    canvas.draw(pen)
    canvas.draw(pen)
    canvas.draw(pen)
    canvas.undo()
    canvas.draw(paintBucket)
    canvas.draw(triangle)
    canvas.draw(eraser)
    canvas.draw(eraser)
    canvas.undo()
    canvas.undo()
    canvas.redo()
    canvas.draw(rectangle)
    canvas.draw(paintBucket)
    canvas.show()
}
```
