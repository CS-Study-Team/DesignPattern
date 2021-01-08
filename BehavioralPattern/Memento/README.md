## 개요
객체의 상태 정보를 저장하고 사용자의 필요에 의하여 원하는 시점의 데이터를 복원할 수 있는 디자인 패턴
예를들어, 게임에서 세이브 기능을 제공해주고 싶을때 유용하게 상요할 수 있다.
주요 구성요소로는 `Originator`, `Caretaker`, `Memento` 3가지가 있다.

## 예제 코드
```kotlin
class Originator {
  private String state;

  public void set(String state) {
    this.state = state;
    System.out.println("Originator: Setting state to " + state);
  }

  public Memento saveToMemento() {
    System.out.println("Originator: Saving to Memento.");
    return new Memento(this.state);
  }

  public void restoreFromMemento(Memento memento) {
    this.state = memento.getSavedState();
    System.out.println("Originator: State after restoring from Memento: " + state);
  }
}

public static class Memento {
  private final String state;

  public Memento(String stateToSave) {
    state = stateToSave;
  }
  
  private String getSavedState() {
    return state;
  }
}
 
class Caretaker {
    public static void main(String[] args) {
      List<Originator.Memento> savedStates = new ArrayList<Originator.Memento>();

      Originator originator = new Originator();
      originator.set("State1");
      originator.set("State2");
      savedStates.add(originator.saveToMemento());
      originator.set("State3");
      savedStates.add(originator.saveToMemento());
      originator.set("State4");

      // 메멘토 패턴의 핵심
      originator.restoreFromMemento(savedStates.get(1));   
    }
}
```

* 실행 결과
```kotlin
Originator: Setting state to State1
Originator: Setting state to State2
Originator: Saving to Memento.
Originator: Setting state to State3
Originator: Saving to Memento.
Originator: Setting state to State4
Originator: State after restoring from Memento: State3
```
