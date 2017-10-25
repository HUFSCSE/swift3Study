# Part 3 함수형 프로그래밍과 스위프트
## CHAPTER 13 클로저

클로저 (Closure) 란 일정 기능을 하는 코드를 하나의 블록으로 모아놓은 것으로, 변수나 상수가 선언된 위치에서 참조를 획득하고 저장할 수 있습니다.

클로저의 모양으로는 다음과 같은 세 가지 형태로 표현할 수 있습니다.

- 이름을 가지면서 어떤 값도 획득하지 않는 전역함수의 형태
- 이름을 가지면서 다른 함수 내부의 값을 획득할 수 있는 중첩된 함수의 형태
- 이름이 없고 주변 문맥에 따라 값을 획득할 수 있는 축약 문법으로 작성된 형태

클로저의 특징으로는 다음과 같은 네 가지 형태로 표현할 수 있습니다.

- 매개변수와 반환 값의 타입을 문맥을 통해 유추할 수 있기 때문에 매개변수와 반환 값의 타입을 생략할 수 있습니다.
- 클로저에 단 한 줄의 표현만 들어있다면 암시적으로 이를 반환값으로 취급합니다.
- 축약된 전달인자 이름을 사용할 수 있습니다.
- 후행 클로저 문법을 사용할 수 있습니다.

  ​


### 13.1 기본 클로저

스위프트의 표준 라이브러리에는 배열의 값을 정렬하기 위해 구현된 sorted(by:) 메서드가 있습니다. 이 메서드는 클로저를 통해 어떻게 정렬할 것인가에 대한 정보를 받아 처리하고 결괏값을 배열로 돌려줍니다. 단순히 정렬만 하기 때문에 입력받은 배열의 타입과 크기가 동일하지만, 기존 입력받은 배열은 변경하지 않고 정렬된 배열을 새로 생성하여 반환합니다.

```swift
public func sorted(by areInIncreasingOrder: (Element, Element) -> Bool) -> [Element]
```

sorted(by:) 메서드는 (배열의 타입과 같은 두 개의 매개변수를 가지며 Bool 타입을 반환하는) 클로저를 전달인자로 받을 수 있습니다. 반환하는 Bool 값은 첫 번째 전달인자 값이 새로 생성되는 배열에서 두 번째 전달인자 값보다 먼저 배치되어야 하는지에 대한 결괏값입니다. true를 반환하면 첫 번째 전달인자가 두 번째 전달인자보다 앞에 옵니다.



```swift
let names: [String] = ["wizplan", "eric", "yagom", "jenny"]

func backwards(first: String, second: String) -> Bool {
    print("\(first) \(second) 비교중 / \(first > second)")
    return first > second
}

let reversed: [String] = names.sorted(by: backwards)
print(reversed)
```

만약 first 문자열이 second 문자열보다 크다면(알파벳이 더 뒤쪽이라면) backwards 함수의 반환값은 true가 될 것입니다. 즉, 값이 더 큰 first 문자열이 second 문자열보다 앞쪽에 정렬되어야 한다는 뜻입니다.

위 메서드의 결과 및 실행순서는 다음과 같습니다.

```swift
eric wizplan 비교중 / false
yagom eric 비교중 / true
yagom wizplan 비교중 / true
jenny eric 비교중 / true
jenny wizplan 비교중 / false
["yagom", "wizplan", "jenny", "eric"]
```

### 실행순서
1. eric과 wizplan을 비교했을 때 false / ["wizplan", "eric"]
2. yagom과 eric을 비교했을 때 true / ["yagom", "wizplan", "eric"] or ["wizplan", "yagom", "eric"]
3. yagom과 wizplan을 비교했을 때 true / ["yagom", "wizplan", "eric"]
4. Jenny와 eric을 비교했을 때 true / ["jenny", yagom", "wizplan", "eric"] or ["yagom", "jenny", wizplan", "eric"] or ["yagom", "wizplan", "jenny", eric"]
5. jenny와 wizplan을 비교했을 때 false / ["yagom", "wizplan", "jenny", eric"]



만약 위 backwards 함수에서 콘솔에 출력하는 구문을 뺀다면 다음과 같은 형태로 표현할 수 있습니다.

```swift
func backwards(first: String, second: String) -> Bool {
    return first > second
}
```
first > second 라는 반환 값을 받기 위해 함수 이름, 매개변수 표현 등 부가적인 표현들을 사용했습니다. 이를 클로저 표현을 사용해서 조금 더 간결하게 표현하겠습니다.

클로저 표현은 통상 아래 형식을 따릅니다. 함수와 마찬가지로 입출력 매개변수를 사용할 수 있지만, 매개변수 기본값은 사용할 수 없습니다.

```swift
{ (매개변수들) -> 반환 타입 in
    실행코드
}
```
이제 backwards(first:second:) 함수를 클로저 표현으로 대체해보겠습니다.

```swift
let reversed: [String] = names.sorted(by: { (first: String, second: String) -> Bool in
    return first > second
})
```
#####장점

- 기존 코드보다 훨씬 간결하고 직관적이다.
- sorted(by:) 메서드로 전달되는 backwards(first:second:) 함수를 찾아다닐 필요가 없다.

#####단점

- 클로저를 모른다면 이해하기 힘들다.



### 13.2 후행 클로저

기본 클로저보다 조금 더 클로저를 읽기 쉽도록 표현을 바꿀 수 있습니다. 후행 클로저처럼 함수나 메서드의 마지막 전달인자로 위치하는 클로저는 함수나 메서드의 소괄호를 닫은 후 작성해도 됩니다. 하나의 클로저만 전달인자로 전달하는 경우에는 소괄호를 생략할 수도 있습니다. 단, 후행 클로저는 맨 마지막 전달인자로 전달되는 클로저에만 해당되므로, 전달인자로 클로저가 여러 개 전달될 때에는 맨 마지막 클로저만 후행 클로저로 사용할 수 있습니다.

```swift
let reversed: [String] = names.sorted() { (first: String, second: String) -> Bool in
    return first > second
}
let reversed: [String] = names.sorted { (first: String, second: String) -> Bool in
    return first > second
}
```


### 13.3 클로저 표현 간소화

#### 13.3.1 문맥을 통한 타입 유추

메서드의 전달인자로 전달되는 클로저는 메서드에서 요구하는 형태로 전달되어야 합니다. 즉, 매개변수의 타입이나 개수, 반환타입 등이 같아야 전달인자로서 전달이 될 수 있습니다. 문맥에 따라 적절히 타입을 유추할 수 있다는 뜻입니다. 그래서 전달인자로 전달되는 클로저를 구현할 때는 매개변수의 타입이나 반환 값의 타입을 생략할 수 있습니다.

```swift
let reversed: [String] = names.sorted { (first, second) -> Bool in
    return first > second
}
```


#### 13.3.2 단축 인자 이름

의미없어 보이는 두 매개변수 이름을 간결하게 표현할 수 있도록 단축 인자 이름을 사용할 수 있습니다. 단축 인자 이름은 첫 번째 전달인자부터 $0, $1, $2, $3… 순서로 표현합니다. 단축 인자 표현을 사용하게 되면 매개변수 및 반환 타입과 실행 코드를 구분하기 위해 사용했던 키워드 in의 사용도 불필요해집니다.

```swift
let reversed: [String] = names.sorted {
    return $0 > $1
}
```


#### 13.3.3 암시적 반환 표현

클로저가 반환 값을 갖는 클로저이고 클로저 내부의 실행문이 한 줄이라면, 암시적으로 그 실행문이 반환 값으로 사용될 수 있습니다.

```swift
let reversed: [String] = names.sorted { $0 > $1 }
```


#### 13.3.4 연산자 함수

비교 연산자(p.105)는 두개의 피연산자를 통해 Bool 타입의 반환을 줍니다. sorted(by:) 메소드에 전달했던 클로저와 동일한 조건입니다. 클로저는 매개변수의 타입과 반환 타입이 연산자가 구현된 함수의 모양과 동일하다면 연산자만 표기하더라도 알아서 연산하고 반환합니다. 그 이유는 연산자가 일종의 함수이자 클로저이기 때문입니다. 따라서, 비교 연산자를 사용하여 다음과 같이 표현할 수 있습니다.

```swift
let reversed: [String] = names.sorted(by: >)
```


### 13.4 값 획득

클로저는 자신이 정의된 위치의 주변 문맥을 통해 상수나 변수를 획득할 수 있습니다. 값 획득을 통해 클로저는 주변에 정의된 상수나 변수가 더는 존재하지 않더라도 그 상수나 변수의 값을 자신 내부에서 참조하거나 수정할 수 있습니다.

중첩 함수도 하나의 클로저 형태로 사용할 수 있는데, 이 중첩 함수 주변의 변수나 상수를 획득해 놓을 수 있습니다. 즉, 자신을 포함하고 있는 함수의 지역변수나 지역상수를 획득할 수 있습니다.

```swift
func makeIncrementer(forIncrement amount: Int) -> (() -> Int) {
    var runningTotal = 0
    func incrementer() -> Int {
        runningTotal += amount
        return runningTotal
    }
    return incrementer
}

let incrementByTwo: (() -> Int) = makeIncrementer(forIncrement: 2)

let first: Int = incrementByTwo() // 2
let second: Int = incrementByTwo() // 4
let third: Int = incrementByTwo() // 6
```
incrementer를 하나 더 생성해주면, incrementerByTwo와는 별개의 다른 참조를 가지는 runningTotal 변숫값을 확인할 수 있습니다.

```swift
let incrementByTwo: (() -> Int) = makeIncrementer(forIncrement: 2)
let incrementByTwo2: (() -> Int) = makeIncrementer(forIncrement: 2)
let incrementByTen: (() -> Int) = makeIncrementer(forIncrement: 10)

let first: Int = incrementByTwo() // 2
let second: Int = incrementByTwo() // 4
let third: Int = incrementByTwo() // 6

let first2: Int = incrementByTwo2() // 2
let second2: Int = incrementByTwo2() // 4
let third2: Int = incrementByTwo2() // 6

let ten: Int = incrementByTen() // 10
let twenty: Int = incrementByTen() // 20
let thirty: Int = incrementByTen() // 30
```


### 13.5 클로저는 참조 타입

함수나 클로저를 상수나 변수에 할당할 때마다 사실은 상수나 변수에 함수나 클로저의 참조를 세팅하는 것입니다. 즉, incrementByTwo라는 상수에 클로저를 할당한다는 것은 클로저의 내용물, 즉 값을 할당하는 것이 아니라 해당 클로저의 참조를 할당하는 것입니다. 결국 클로저의 참조를 다른 상수에 할당해준다면 이는 두 상수가 모두 같은 클로저를 가리킨다는 뜻입니다.

```swift
let incrementByTwo: (() -> Int) = makeIncrementer(forIncrement: 2)
let sameWithIncrementByTwo: (() -> Int) = incrementByTwo

let first: Int = incrementByTwo() // 2
let second: Int = sameWithIncrementByTwo() // 4
```


### 13.6 탈출 클로저

함수의 전달인자로 전달된 클로저가 함수 종료 후에 호출될 때 클로저가 함수를 탈출한다고 표현합니다. 클로저를 매개변수로 갖는 함수를 선언할 때 매개변수 이름의 콜론 뒤에 @escaping 키워드를 사용하여 클로저가 탈출하는 것을 허용한다고 명시해줄 수 있습니다.

기존 sorted(by:) 메서드를 비롯하여 계속 살펴보았던 함수에는 @escaping 키워드를 찾아볼 수 없었는데, 정렬될 요소를 비교연산하기 위해 전달인자로 전달하는 클로저는 비탈출 클로저이기 때문입니다. @escaping 키워드를 따로 명시하지 않는다면 매개변수로 사용되는 클로저는 기본으로 비탈출 클로저이며, 함수의 동작이 끝난 후 전달된 클로저가 필요 없을 때 사용합니다.

클로저가 함수를 탈출할 수 있는 경우 중 하나는 함수 외부에 정의된 변수나 상수에 저장되어 함수가 종료된 후에 사용될 경우입니다. 예를 들어 비동기적으로 작업을 수행하기 위해서 컴플리션 핸들러를 클로저의 형태로 전달인자를 통해 받는 함수들이 많습니다. 함수가 작업을 종료하고 난 이후(즉, 함수의 return 후)에 클로저가 호출되기 때문에 클로저는 함수를 탈출해 있어야만 합니다. 함수의 전달인자로 전달받은 클로저를 다시 반환할 때에도 마찬가지입니다.

다음은 탈출 클로저를 매개변수로 갖는 함수들입니다.

```swift
var completionHandlers: [() -> Void] = []
func someFunctionWithEscapingClosure(completionHandler: @escaping () -> Void) {
    completionHandlers.append(completionHandler)
}
```
```swift
typealias VoidVoidClosure = () -> Void

let firstClosure: VoidVoidClosure = {
    print("Closure A")
}

let secondClosure: VoidVoidClosure = {
    print("Closure B")
}

func returnOneClosure(first: @escaping VoidVoidClosure, second: @escaping VoidVoidClosure, shouldReturnFirstClosure: Bool) -> VoidVoidClosure {
    return shouldReturnFirstClosure ? first : second
}

let returnedClosure: VoidVoidClosure = returnOneClosure(first: firstClosure, second: secondClosure, shouldReturnFirstClosure: true)

returnedClosure() // Closure A

var closures: [VoidVoidClosure] = []

func appendClosure(closure: @escaping VoidVoidClosure) {
    closures.append(closure)
}
```
두 함수의 전달인자로 전달되는 클로저 앞에 @escaping 키워드를 명시하여 탈출 클로저임을 명시해주어야 합니다. 이 코드는 클로저가 모두 탈출할 수 있는 조건이 명확하기 때문에 @escaping 키워드를 명시하여 탈출 클로저임을 명시하지 않으면 컴파일 오류가 발생합니다.

타입 내부 메서드의 매개변수 클로저에 @escaping 키워드를 사용하여 탈출 클로저임을 명시한 경우, 클로저 내부에서 해당 타입의 프로퍼티나 메서드, 서브스크립트 등에 접근하려면 self 키워드를 명시적으로 사용해야 합니다. 비탈출 클로저는 클로저 내부에서 타입 내부의 프로퍼티나 메서드, 서브스크립트 등에 접근할 때 self 키워드를 꼭 써주지 않아도 됩니다.

```swift
typealias VoidVoidClosure = () -> Void

func functionWithNoescapeClosure(closure: VoidVoidClosure) {
    closure()
}

func functionWithEscapingClosure(completionHandler: @escaping VoidVoidClosure) -> VoidVoidClosure {
    return completionHandler
}

class SomeClass {
    var x = 10
    
    func runNoescapeClosure() {
        functionWithNoescapeClosure { x = 200 }
    }
    
    func runEscapingClosure() -> VoidVoidClosure {
        return functionWithEscapingClosure { self.x = 100 }
    }
    
}

let instance: SomeClass = SomeClass()
instance.runNoescapeClosure()
print(instance.x) // 200

let returnedClosure: VoidVoidClosure = instance.runEscapingClosure()
returnedClosure()
print(instance.x) // 100
```


### 13.7 자동 클로저

함수의 전달인자로 전달되는 표현을 자동으로 변환해주는 클로저를 자동 클로저라고 합니다. 자동클로저의 특징은 다음과 같습니다.

- 전달인자를 갖지 않습니다.
- 호출되었을 때 자신이 감싸고 있는 코드의 결괏값을 반환합니다.
- 함수로 전달되는 클로저를 (소괄호와 중괄호를 겹쳐서 써야하는) 어려운 클로저 문법을 사용하지 않고도 클로저로 사용할 수 있도록 문법적 편의를 제공합니다.
- 클로저가 호출되기 전까지는 클로저 내부의 코드는 동작하지 않습니다. 따라서 연산을 지연시킬 수 있고, 이 과정은 연산에 자원이 많이 소모된다거나 부작용이 우려될 때 유용하게 사용할 수 있습니다.

다음은 클로저를 이용한 연산 지연을 실행하는 함수입니다.

```swift
var customersInLine: [String] = ["YoangWha", "SangYong", "SungHun", "HaMi"]
print(customersInLine.count) // 4

let customerProvider: () -> String = {
    return customersInLine.removeFirst()
}
print(customersInLine.count) // 4

print("Now serving \(customerProvider())!")
print(customersInLine.count) // 3
```
다음은 함수의 전달인자로 전달되는 클로저를 이용한 연산 지연을 실행하는 함수입니다.

```swift
var customersInLine: [String] = ["YoangWha", "SangYong", "SungHun", "HaMi"]
print(customersInLine.count) // 4

let customerProvider: () -> String = {
    return customersInLine.removeFirst()
}
print(customersInLine.count) // 4

func serveCustomer(_ customerProvider: () -> String) {
    return print("Now serving \(customerProvider())!")
}
serveCustomer( { customersInLine.removeFirst() } )
print(customersInLine.count) // 3
```
다음은 자동 클로저를 이용한 연산 지연을 실행하는 함수입니다.

```swift
var customersInLine: [String] = ["YoangWha", "SangYong", "SungHun", "HaMi"]
print(customersInLine.count) // 4

let customerProvider: () -> String = {
    return customersInLine.removeFirst()
}
print(customersInLine.count) // 4

func serveCustomer(_ customerProvider: @autoclosure () -> String) {
    return print("Now serving \(customerProvider())!")
}
serveCustomer(customersInLine.removeFirst())
print(customersInLine.count) // 3
```
기존의 serveCustomer(_:) 함수와 동일한 역할을 수행하지만 매개변수에 @autoclosure 속성을 주었기 때문에 자동 클로저 기능을 사용합니다. 자동 클로저 속성을 부여한 매개변수는 클로저 대신에 customersInLine.removeFirst() 코드의 실행 결과인 String 타입의 문자열을 전달인자로 받게 됩니다. String 타입의 값이 자동 클로저 매개변수에 전달되면 String 값을 매개변수가 없는 String 값을 반환하는 클로저로 변환해줍니다. 자동 클로저는 전달인자를 갖지 않기 때문에 반환 타입의 값이 자동 클로저의 매개변수로 전달되면 이를 클로저로 바꿔줄 수 있는 것입니다. 이렇게 String으로 전달된 전달인자가 자동으로 클로저로 변환되기 때문에 자동 클로저라고 부릅니다.

기본적으로 @autoclosure 속성은 @noescape 속성을 포함합니다. 만약 자동 클로저를 탈출하는 클로저로 사용하고 싶다면 @autoclosure 속성 뒤에 @escaping 속성을 덧붙여서 사용하면 됩니다.

```swift
var customersInLine: [String] = ["YoangWha", "SangYong", "SungHun", "HaMi"]
print(customersInLine.count) // 4

func returnProvider(_ customerProvider: @autoclosure @escaping () -> String) -> (() -> String) {
    return customerProvider
}

let customerProvider: () -> String = returnProvider(customersInLine.removeFirst())
print("Now serving \(customerProvider())!")
print(customersInLine.count) // 3
```
