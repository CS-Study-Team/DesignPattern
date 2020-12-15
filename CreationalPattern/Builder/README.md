# 빌더패턴
---

**객체**의 **생성**과 **조립 방법**을 분리한다.
- 여기서 **객체**란 아래의 참여자 중 하나인 Product를 일컫는다.
- **생성**은 Builder가 담당한다. (Product의 각 부품 생성)
- **조립 방법** 정의는 Builder를 이용한 Director의 지시로 볼 수 있다.

<br>

빌더패턴의 참여자로는
- Director : Builder가 만든 부품을 조합해 Product를 만드는 역할
- Builder : Builder 인터페이스
- ConcreteBuilder : Builder 인터페이스 구현체
- Product : Director가 Builder가 만든 부품을 조합해 만든 결과물

가 있다.

<br>

### 예시
RTFReader(Director)는 RTF 문서를 판독한 후(parse) 문서 변환(convert)을 거쳐 ASCIIText(Product)를 얻고 싶다. 빌더패턴을 이용하면, <br>
- RTFReader내부에서 RTF 문서의 parse 작업이 이루어진다.
- parse된 작업물을 RTFReader의 지시에 따라 Builder가 변환하는 작업을 한다.
- ASCIIText가 만들어진다. 


```cpp
// CreateASCIIText 메소드에는 Director인 RTFReader가 Builder에게 한 지시가 정의되어 있다.
ASCIIText* RTFReader::CreateASCIIText(ASCIIConverter builder){
    builder.BuildText(); // 먼저 아무것도 가미되지 않은 original Text를 생성.
    builder.BuildFont();
    builder.BuildPartA();
    builder.BuildPartB();
    // Director는 필요한 요소를 만들어 달라고 계속 builder에 요청한다.
    return builder.GetText();
};
```

ASCIIConverter는 Builder 인터페이스를 구현한 ConcreteBuilder이다. Builder 인터페이스는 Product의 생성 과정을 반영한다. 아래는 ASCIIConverter의 모체인 Converter라는 Builder 인터페이스이다.
```cpp
class Converter {
    public:
      virtual void BuildText(){}
      virtual void BuildFont(){} // Product에 필요한 부품을 생성한다.
      virtual void BuildPartA(){}
      virtual void BuildPartB(){}

      virtual Text* GetText(){ return 0; }

    protected:
      Converter();
};
```
 
ASCIIConverter는 이를 implements한다.
```cpp
class ASCIIConverter : public Converter {
    public:
      ASCIIConverter();

      virtual void BuildText();
      virtual void BuildFont();
      virtual void BuildPartA();
      virtual void BuildPartB();

      virtual Text* GetText();

      private:
        Text* _currentText;
};
```

`BuildText()`로 Text의 인스턴스를 생성하여 `_currentText`로 세팅하고, 다른 연산을 통해 부품들을 생성 및 복합한 후 `GetText()`로 그 결과를 사용자에게 돌려준다.
```cpp
ASCIIConverter::ASCIIConverter(){
    _currentText = 0;
}

void ASCIIConverter::BuildText(){
    _currentText = new Text;
}

void ASCIIConverter::GetText(){
    return _currentText;
}
```

또한, RTFReader(Director)는 ASCIIText외에 다른 종류의 변환된 Text를 얻고 싶을수도 있다. 예를 들어 HTML 포맷의 Text를 얻고 싶다고 가정하자. 이럴 때는 Converter(Builder) 인터페이스를 상속하여 HTMLConverter라는 ConcreteBuilder를 구현하면 된다. HTMLConverter를 `RTFReader::CreateHTMLText(HTMLConverter builder)`메소드의 매개변수로 넘기고 RTFReader에서 HTMLText(Product)의 구현 방법을 `builder`에 지시하여, HTMLText의 생성을 완성할 수 있다.

사용자는 최종적으로 Director와 ConcreteBuilder를 아래와 같이 이용할 수 있다.
```cpp
Text* asciiText;
Text* htmlText;

RTFReader reader;

ASCIIConverter asciiConverter;
HTMLConverter htmlConverter;

asciiText = reader.CreateASCIIText(asciiConverter);
htmlText = reader.CreateHTMLText(htmlConverter);
```
