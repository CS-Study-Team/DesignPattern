## 개요
어떤 언어에 대해, 그 언어의 문법에 대한 표현을 정의하면서 그것(표현)을 사용하여 해당 언어로 기술된 문장을 해석하는 해석자를 함께 정의한다.

## 동기
특정한 종류의 문제가 자주 발생할 때는, 어떤 간결한 언어를 써서 그 문제를 문장으로 표현하는 것이 나을 수 있다. 그리고 나서 그 문장을 해석하는 해석자를 만들어 문장을 해석하게 하여 문제를 해결한다. 이때 해결에 필요한 코드를 작성하기보단 간단한 언어로 문장을 반복적으로 작성하기만 하면 해결 될 수 있다.

예를들면 다음과 같다. 어떤 패턴에 일치되는 문자열을 찾는 문제는 일반적인 문제에 속한다. 이때, 정규 표현식은 문자열 패턴을 정의하는 데 사용되는 표준 언어다. 문자열에서 어떤 패턴을 찾는 알고리즘을 매번 작성하는 것보다, 발견할 문자열을 정의하는 정규 표현식을 해석하는 알고리즘을 한 번 만드는 것이 더 낫다.

해석자 패턴은 간단한 언어의 문법을 정의하는 방법과 그 언어로 문장을 구성하는 방법, 이들 문장을 해석하는 방법을 설명한다.

## 활용성
* 구문 분석 생성기는 추상 구문 트리를 생성하지 않고도 문장을 해석 할 수 있기 때문에, 시간과 공간을 절약 할 수 있다.
* 일차적으로 구문 분석 트리를 다른 형태로 번역할 때.

## 예제코드
```kotlin
sealed class Expression {
    class Constant(val value: Int) : Expression()
    class Add(val left: Expression, val right: Expression) : Expression()
    class Mul(val left: Expression, val right: Expression) : Expression()
    class Interpreter {
        fun interpret(expression: Expression): Int = when (expression) {
            is Mul -> interpret(expression.left) * interpret(expression.right)
            is Add -> interpret(expression.left) + interpret(expression.right)
            is Constant -> expression.value
        }
    }

    fun toInt(): Int = Interpreter().interpret(this)
}

fun main() {
    println(Expression.Constant(2).toInt())
    println(Expression.Add(Expression.Constant(2), Expression.Constant(3)).toInt())
    println(Expression.Mul(Expression.Constant(2), Expression.Constant(3)).toInt())
}
```