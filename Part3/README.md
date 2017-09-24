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



## CHAPTER 14 옵셔널 체이닝과 빠른종료

### 14.1 옵셔널 체이닝

옵셔널 체이닝은 nil일지도 모르는 옵셔널에 속해 있는 프로퍼티, 메서드, 서브스크립션 등을 가져오거나 호출할 때 사용할 수 있는 일련의 과정입니다. 옵셔널이 값을 가지고 있다면 프로퍼티, 메서드, 서브스크립트 등을 호출할 수 있고, 옵셔널이 nil이라면 프로퍼티, 메서드, 서브스크립트 등은 nil을 반환합니다. 옵셔널을 반복 사용하여 옵셔널이 자전거 체인처럼 서로 꼬리를 물고있는 모양이기 때문에 옵셔널 체이닝이라고 부릅니다. 자전거 체인에서 한 칸이라도 없거나 고장 나면 체인 전체가 동작하지 않듯이 중첩된 옵셔널 중 하나라도 값이 존재하지 않으면 결과적으로 nil을 반환합니다.

옵셔널 체이닝은 프로퍼티나 메서드 또는 서브스크립트를 호출하고 싶은 옵셔널 변수나 상수 뒤에 물음표(?)를 붙여 표현합니다. 옵셔널이 nil이 아니라면 정상적으로 호출될 것이고, nil이라면 결괏값으로 nil을 반환할 것입니다. 결과적으로 nil이 반환될 가능성이 있으므로 옵셔널 체이닝의 반환된 값은 항상 옵셔널입니다.

물음표 대신에 느낌표(!)를 사용할 수도 있는데 이는 옵셔널에서 값을 강제 추출하는 효과가 있습니다. 물음표를 사용하는 것과 가장 큰 차이점이라고 한다면 값을 강제 추출을 하기 때문에 옵셔널에 값이 없다면 런타임 오류가 발생한다는 점입니다. 또 다른 점은 옵셔널에서 값이 강제 추출되어 반환되기 때문에 반환 값이 옵셔널이 아니라는 점입니다. 하지만 정말 100% nil이 아니라는 확신을 하더라도 사용을 지양하는 편이 좋습니다.

옵셔널 체이닝에 대해 알아보기 위해 기본 클래스 설계를 하겠습니다.

```swift
class Room { // 호실
    var number: Int // 호실 번호
    
    init(number: Int) {
        self.number = number
    }
}

class Building { // 건물
    var name: String // 건물 이름
    var room: Room? // 호실 정보
    
    init(name: String) {
        self.name = name
    }
}

struct Address { // 주소
    var province: String // 광역시/도
    var city: String // 시/군/구
    var street: String // 도로명
    var building: Building? // 건물
    var detailAddress: String? // 건물 외 주소
}

class Person { // 사람
    var name: String // 이름
    var address: Address? // 주소
    
    init(name: String) {
        self.name = name
    }
}
```
사람의 정보를 표현하기 위해 Person 클래스를 설계했습니다. Person 클래스는 이름이 있으며 주소를 옵셔널로 갖습니다. 주소 정보는 Address 구조체로 설계했습니다. 주소에는 광역시/도, 시/군/구, 도로명이 필수이며, 건물 정보가 있거나 건물이 아니라면 상세주소를 기재할 수 있도록 했습니다. 건물 정보는 Building 클래스로 설계했습니다. 건물은 이름이 있으며, 호실의 정보를 가집니다. 그리고 호실 정보는 Room 클래스로 설계했으며 각 호실은 번호를 갖습니다.

먼저, yagom 이라는 사람의 인스턴스를 생성합니다.

```swift
let yagom: Person = Person(name: "yagom")
```
yagom이 사는 호실 번호를 알고 싶을 때, 옵셔널 체이닝과 강제 추출을 사용하여 프로퍼티에 접근해보면 다음과 같은 결과를 볼 수 있습니다.

```swift
let yagomRoomViaOptionalChaining: Int? = yagom.address?.building?.room?.number // nil
let yagomRoomViaOptionalUnwraping: Int = yagom.address!.building!.room!.number // 오류 발생
```
yagom 이라는 사람의 인스턴스에는 아직 주소 정보와 건물 정보, 호실 정보가 없습니다. yagomRoomViaOptionalChaining 상수에 호실 번호를 할당하기 위해 옵셔널 체이닝을 사용하면 yagom의 address 프로퍼티가 nil이므로 옵셔널 체이닝 도중 nil이 반환됩니다. 그러나 yagomRoomViaOptionalUnwraping 상수에 호실 번호를 할당할 때는 강제 추출을 시도했기 때문에 nil인 address 프로퍼티에 접근하려 할 때 런타임 오류가 발생합니다. (p.256의 그림 14-1 참조)

다음은 옵셔널 바인딩을 사용하여 yagom이 사는 호실 정보를 가져오는 코드를 표현한 것입니다.

```swift
let yagom: Person = Person(name: "yagom")

var roomNumber: Int? = nil

if let yagomAddress: Address = yagom.address {
    if let yagomBuilding: Building = yagomAddress.building {
        if let yagomRoom: Room = yagomBuilding.room {
            roomNumber = yagomRoom.number
        }
    }
}

if let number: Int = roomNumber {
    print(number)
} else {
    print("Can not find room number")
}
```
이를 옵셔널 체이닝으로 표현해보면 훨씬 간단해집니다.

```swift
let yagom: Person = Person(name: "yagom")

if let roomNumber: Int = yagom.address?.building?.room?.number {
    print(roomNumber)
} else {
    print("Can not find room number")
}
```
옵셔널 체이닝의 결괏값은 옵셔널 값이기 때문에 옵셔널 바인딩과 결합하여 yagom.address?.building?.room?.number의 결괏값이 nil이 아님을 확인하는 동시에 roomNumber라는 상수로 받아올 수 있습니다.

이처럼 옵셔널 체이닝을 통해 한 단계 뿐만 아니라 여러 단계를 통해 복잡하게 중첩된 옵셔널 프로퍼티나 메서드 등에 매번 nil 체크를 하지 않아도 손쉽게 접근할 수 있습니다. 또한, 옵셔널 체이닝을 통해 값을 받아오기만 하는 것이 아니라 반대로 값을 할당해줄 수도 있습니다.

```swift
yagom.address?.building?.room?.number = 505
print(yagom.address?.building?.room?.number) // nil
```
하지만 아직 yagom의 address 프로퍼티가 없으며 그 하위의 building 프로퍼티도, room 프로퍼티도 없습니다. 그렇기 때문에 해당 옵셔널 체이닝은 동작 도중에 중지될 것이며, number 프로퍼티는 존재조차 하지 않으므로 505가 할당되지 않습니다.

```swift
yagom.address = Address(province: "충청북도", city: "청주시 청원구", street: "충청대로", building: nil, detailAddress: nil)
yagom.address?.building = Building(name: "곰굴")
yagom.address?.building?.room = Room(number: 0)
yagom.address?.building?.room?.number = 505

print(yagom.address?.building?.room?.number) // Optional(505)
```
위와 같이 옵셔널 체인에 존재하는 프로퍼티를 실질적으로 할당해준 후 옵셔널 체이닝을 통해 값이 정상적으로 반환되는 것을 확인할 수 있습니다.

옵셔널 체이닝을 통해 메서드와 서브스크립트(p.291) 호출도 가능합니다. 서브스크립트는 인덱스를 통해 값을 넣고 빼올 수 있는 기능입니다.

먼저, 옵셔널 체이닝을 통한 메서드 호출입니다. 호출 방법은 프로퍼티 호출과 동일합니다. 만약 메서드의 반환 타입이 옵셔널이라면 이 또한 옵셔널 체인에서 사용 가능합니다. Address 구조체에 메서드 코드를 추가하고 옵셔널 체인을 통해 호출해봅시다.

```swift
class Room { // 호실
    var number: Int // 호실 번호
    
    init(number: Int) {
        self.number = number
    }
}

class Building { // 건물
    var name: String // 건물 이름
    var room: Room? // 호실 정보
    
    init(name: String) {
        self.name = name
    }
}

struct Address { // 주소
    var province: String // 광역시/도
    var city: String // 시/군/구
    var street: String // 도로명
    var building: Building? // 건물
    var detailAddress: String? // 건물 외 주소
    
    init(province: String, city: String, street: String) {
        self.province = province
        self.city = city
        self.street = street
    }
    
    func fullAddress() -> String? {
        var restAddress: String? = nil
        
        if let buildingInfo: Building = self.building {
            restAddress = buildingInfo.name
        } else if let detail = self.detailAddress {
            restAddress = detail
        }
        
        if let rest: String = restAddress {
            var fullAddress: String = self.province
            
            fullAddress += " " + self.city
            fullAddress += " " + self.street
            fullAddress += " " + rest
            
            return fullAddress
        } else {
            return nil
        }
    }
    
    func printAddress() {
        if let address: String = self.fullAddress() {
            print(address)
        }
    }
}

class Person { // 사람
    var name: String // 이름
    var address: Address? // 주소
    
    init(name: String) {
        self.name = name
    }
}

let yagom: Person = Person(name: "yagom")

yagom.address = Address(province: "충청북도", city: "청주시 청원구", street: "충청대로")
yagom.address?.building = Building(name: "곰굴")
yagom.address?.building?.room = Room(number: 0)
yagom.address?.building?.room?.number = 505

yagom.address?.fullAddress()?.isEmpty // false
yagom.address?.printAddress() // 충청북도 청주시 청원구 충청대로 곰굴
```
서브스크립트를 가장 많이 사용하고 있는 곳은 Array와 Dictionary입니다. 옵셔널의 서브스크립트를 사용하고자 할 때는 대괄호([])보다 앞에 물음표(?)를 표기해주어야합니다. 이는 서브스크립트 외에도 언제나 옵셔널 체이닝을 사용할 때의 규칙입니다.

```swift
let optionalArray: [Int]? = [1,2,3]
optionalArray?[1] // 2

var optionalDictionary: [String: [Int]]? = [String: [Int]]()
optionalDictionary?["numberArray"] = optionalArray
optionalDictionary?["numberArray"]?[2] // 3
```
### 14.2 빠른종료

빠른종료의 핵심 키워드는 guard입니다. guard 구문은 if 구문과 유사하게 Bool 타입의 값으로 동작하는 기능으로, guard 뒤에 따라붙는 코드의 실행 결과가 true일 때 코드가 계속 실행됩니다. if 구문과는 다르게 guard 구문은 항상 else 구문이 뒤에 따라와야 합니다. 만약 guard 뒤에 따라오는 Bool 값이 false 라면 else의 블록 내부 코드를 실행하게 되는데, 이때 else 구문의 블록 내부에는 꼭 자신보다 상위의 코드 블록을 종료하는 코드가 들어가게 됩니다. 이렇게 현재의 코드 블록을 종료할 시에는 return, break, continue, throw 등의 제어문 전환 명령을 사용합니다. 또는 fatalError()와 같은 비반환 함수나 메서드를 호출할 수도 있습니다.

```swift
guard Bool 타입 값 else {
    예외사항 실행문
  	제어문 전환 명령어
}
```
guard 구문을 사용하면 if 코드를 훨씬 간결하고 읽기 좋게 구성할 수 있습니다. if 구문을 쓰면 예외사항을 else 블록으로 처리해야 하지만 예외사항만을 처리하고 싶다면 guard 구문을 쓰는 것이 훨씬 간편합니다. 다음은 같은 기능을 수행하기 위한 if 구문과 guard 구문의 비교입니다.

```swift
for i in 0...3 {
    if i == 2{
        print(i)
    } else {
        continue
    }
} // if

for i in 0...3 {
    guard i == 2 else {
        continue
    }
    print(i)
} // guard
```
Bool 타입의 값으로 guard 구문을 동작시킬 수 있지만 옵셔널 바인딩의 역할도 수행할 수 있습니다. guard 뒤에 따라오는 옵셔널 바인딩 표현에서 옵셔널의 값이 있는 상태라면 guard 구문에서 옵셔널 바인딩된 상수를 guard 구문이 실행된 아래 코드부터 함수 내부의 지역상수처럼 사용할 수 있습니다.

```swift
func greet(_ person: [String: String]) {
    guard let name: String = person["name"] else {
        return
    }
    
    print("Hello \(name)!")
    
    guard let location: String = person["location"] else {
        print("I hope the weather is nice near you")
        return
    }
    
    print("I hope the weather is nice in \(location)")
}

var personInfo: [String : String] = [String : String]()
personInfo["name"] = "Jenny"

greet(personInfo)
// Hello Jenny!
// I hope the weather is nice near you

personInfo["location"] = "Korea"

greet(personInfo)
// Hello Jenny!
// I hope the weather is nice in Korea
```
위와 같이 guard를 통해 옵셔널 바인딩 된 상수는 greet(_:) 함수 내에서 지역상수처럼 사용된 것을 볼 수 있습니다.

기존 옵셔널 체이닝(p.253)에서 작성했던 코드(p.261) 중 Address 구조체의 fullAddress() 메서드를 다음과 같이 수정할 수도 있습니다.

```swift
    func fullAddress() -> String? {
        var restAddress: String? = nil
        
        if let buildingInfo: Building = self.building {
            restAddress = buildingInfo.name
        } else if let detail = self.detailAddress {
            restAddress = detail
        }
        
        guard let rest: String = restAddress {
            return nil
        }
        
        var fullAddress: String = self.province
        
        fullAddress += " " + self.city
        fullAddress += " " + self.street
        fullAddress += " " + rest
        
        return fullAddress
    }
```
조금 더 구체적인 조건을 추가하고 싶다면 쉼표(,)로 추가조건을 나열해주면 됩니다. 추가된 조건은 Bool 타입 값이어야 합니다. 또 쉼표로 추가된 조건은 AND 논리연산과 같은 결과이므로 쉼표를 &&로 치환할 수 있습니다.

```swift
func enterClub(name: String?, age: Int) {
    guard let name: String = name, let age: Int = age, age > 19, name.isEmpty == false else {
        print("You are too young to enter the club")
        return
    }
    
    print("Welcome \(name)")
}

enterClub(name: "name1", age: 10) // "You are too young to enter the club"
enterClub(name: "name2", age: 30) // "Welcome name2"
```
guard 구문의 한계는 자신을 감싸는 코드 블록, 즉 return, break, continue, throw 등의 제어문 전환 명령어를 쓸 수 없는 상황이라면 사용이 불가능하다는 점입니다. 함수나 메서드, 반복문 등 특정 블록 내부에 위치하지 않는다면 사용이 제한됩니다.

```swift
let first: Int = 3
let second: Int = 5

guard first > second else {
    // 여기에 들어올 제어문 전환 명령이 없으므로 오류 발생
}
```


## CHAPTER 15 맵, 필터, 리듀스

스위프트는 함수를 일급 객체로 취급하고, 다른 함수의 전달인자로 사용할 수 있습니다. 매개변수로 함수를 갖는 함수를 고차함수라고 부르는데, 스위프트에 유용한 대표적인 고차함수로는 맵, 필터, 리듀스 등이 있습니다. 이런 고차함수를 통해 데이터의 연산을 쉽게 수행하는 방법을 알아보겠습니다.

### 15.1 맵

맵(Map)은 자신을 호출할 때 매개변수로 전달된 함수를 실행하여 그 결과를 다시 반환해주는 함수로, 배열, 딕셔너리, 세트, 옵셔널 등에서 사용할 수 있습니다. 조금 더 정확히 말하자면 스위프트의 Sequence, Collection 프로토콜을 따르는 타입과 옵셔널에서 사용이 가능합니다. 맵을 사용하면 컨테이너가 담고 있던 각각의 값을 매개변수를 통해 받은 함수에 적용한 후 다시 컨테이너에 포장하여 반환합니다. 기존 컨테이너의 값은 변경되지 않고 새로운 컨테이너가 생성되어 반환됩니다. 그래서 맵은 기존 데이터를 변형하는데 많이 사용됩니다.

map 메서드의 사용법은 기존 for-in 구문과 별 차이가 없지만, 코드의 재사용 측면이나 컴파일러 최적화 측면에서 본다면 성능 차이가 있습니다. 또, 다중 스레드 환경일 때 대상 컨테이너의 값이 스레드에서 변경되는 시점에 다른 스레드에서도 동시에 변경되려고 할 때 예측치 못한 결과가 발생하는 부작용을 방지할 수도 있습니다. 다음은 같은 기능을 수행하기 위한 for-in 구문과 map 메서드 사용의 비교입니다.

```swift
let numbers: [Int] = [0, 1, 2, 3, 4]

var doubledNumbers: [Int] = [Int]()
var strings: [String] = [String]()

for number in numbers {
    doubledNumbers.append(number * 2)
    strings.append("\(number)")
} // for-in

print(doubledNumbers) // [0, 2, 4, 6, 8]
print(strings) // ["0", "1", "2", "3", "4"]

doubledNumbers = numbers.map({ (number: Int) -> Int in
    return number * 2
})
strings = numbers.map({ (number: Int) -> String in
    return "\(number)"
}) // map

print(doubledNumbers) // [0, 2, 4, 6, 8]
print(strings) // ["0", "1", "2", "3", "4"]
```
map 메서드를 사용했을 때 for-in 구문을 사용한 것보다 간결하고 편리하게 각 요소의 연산을 수행하는 것을 볼 수 있습니다. 또한, for-in 구문을 사용하기 위하여 빈 배열을 처음 생성해주는 작업도 필요 없으며, 배열의 append 연산을 수행하기 위한 시간도  필요 없습니다. 해당 map 메서드의 모식도는 p.256의 그림 15-2 에서 확인할 수 있습니다.

map 메서드를 기존 클로저 표현식 및 후행 클로저 표현식을 사용하여 표현을 더 간략화해볼 수 있습니다.

```swift
let numbers: [Int] = [0, 1, 2, 3, 4]

// 기본 클로저 표현식
var doubledNumbers = numbers.map { (number: Int) -> Int in
    return number * 2
}
print(doubledNumbers) // [0, 2, 4, 6, 8]

// 매개변수 및 반환 타입 생략
doubledNumbers = numbers.map({ return $0 * 2 })
print(doubledNumbers) // [0, 2, 4, 6, 8]

// 반환 키워드 생략
doubledNumbers = numbers.map({ $0 * 2 })
print(doubledNumbers) // [0, 2, 4, 6, 8]

// 후행 클로저 사용
doubledNumbers = numbers.map { $0 * 2 }
print(doubledNumbers) // [0, 2, 4, 6, 8]
```
클로저 표현을 간략화하니 코드가 간결해졌지만, 같은 기능을 여러 번 사용할 것이라면 하나의 클로저로 여러 map 메서드에서 재사용하는 편이 좋을 것 같습니다. 따라서, 다음과 같이 재사용 가능한 코드로 재구성해볼 수 있습니다.

```swift
let evenNumbers: [Int] = [0, 2, 4, 6, 8]
let oddNumbers: [Int] = [0, 1, 3, 5, 7]
let multiplyTwo: (Int) -> Int = { $0 * 2 }

let doubledEvenNumbers = evenNumbers.map(multiplyTwo)
print(doubledEvenNumbers) // [0, 4, 8, 12, 16]

let doubledOddNumbers = oddNumbers.map(multiplyTwo)
print(doubledOddNumbers) // [0, 2, 6, 10, 14]
```
또한 map 메서드는 다음과 같이 여러 컨테이너 타입에 모두 적용할 수도 있습니다.

```swift
let alphabetDictionary: [String : String] = ["a":"A", "b":"B"]

var keys: [String] = alphabetDictionary.map { (tuple: (String, String)) -> String in
        return tuple.0
}

keys = alphabetDictionary.map { $0.0 }

let values: [String] = alphabetDictionary.map { $0.1 }
print(keys) // ["b", "a"]
print(values) // ["B", "A"]

var numberSet: Set<Int> = [1, 2, 3, 4, 5]
let resultSet = numberSet.map { $0 * 2 }
print(resultSet) // [10, 4, 6, 2, 8]

let optionalInt: Int? = 3
let resultInt: Int? = optionalInt.map { $0 * 2 }
print(resultInt) // Optional(6)

let range: CountableClosedRange = (0...3)
let resultRange: [Int] = range.map { $0 * 2 }
print(resultRange) // [0, 2, 4, 6]
```


### 15.2 필터

필터(Filter)는 컨테이너 내부의 값을 걸러서 추출하는 함수로, 맵과 마찬가지로 새로운 컨테이너에 값을 담아 반환합니다. 다만, 기존 콘텐츠를 변형하는 것이 아닌, 기존 콘텐츠를 특정 조건에 맞게 걸러내는 역할을 할 수 있습니다. filter 함수의 매개변수로 전달되는 함수의 반환 타입은 Bool 으로 반환 값이 true면 새로운 컨테이너에 포함되고, false면 포함하지 않습니다.

```swift
let numbers: [Int] = [0, 1, 2, 3, 4, 5]

let evenNumbers: [Int] = numbers.filter { (number: Int) -> Bool in
    return number % 2 == 0
}
print(evenNumbers) // [0, 2, 4]

let oddNumbers: [Int] = numbers.filter { $0 % 2 != 0 }
print(oddNumbers) // [1, 3, 5]
```
다음과 같이 map 메서드와 filter 메서드를 같이 사용할 수도 있습니다.

```swift
let numbers: [Int] = [0, 1, 2, 3, 4, 5]

let mappedNumbers: [Int] = numbers.map { $0 + 3 }

let evenNumbers: [Int] = mappedNumbers.filter { (number: Int) -> Bool in
	return number % 2 == 0
}
print(evenNumbers) // [4, 6, 8]

let oddNumbers: [Int] = numbers.map { $0 + 3 }
                                .filter { $0 % 2 != 0 }
print(oddNumbers) // [3, 5, 7]
```


### 15.3 리듀스

리듀스(Reduce)는 컨테이너 내부의 콘텐츠를 하나로 합쳐주는 함수로, 정수 배열이라면 정수 배열의 모든 값을 전달인자로 전달받은 함수의 연산 결과로 합쳐주고, 문자열 배열이라면 문자열을 하나로 합쳐줍니다. initial이라는 이름의 매개변수로 전달되는 값을 통해 초깃값을 지정해줄 수 있습니다.

```swift
let numbers: [Int] = [1, 2, 3]

// 초깃값이 0이고 정수 배열의 모든 값을 더합니다.
var sum: Int = numbers.reduce(0, { (first: Int, second: Int) -> Int in
    print("\(first) + \(second)")
    // 0 + 1
    // 1 + 2
    // 2 + 3
    return first + second
})
print(sum) // 6
print()

// 초깃값이 0이고 정수 배열의 모든 값을 뺍니다.
var subtract: Int = numbers.reduce(0, { (first: Int, second: Int) -> Int in
    print("\(first) - \(second)")
    // 0 - 1
    // -1 - 2
    // -3 - 3
    return first - second
})
print(subtract) // -6
print()

// 초깃값이 3이고 정수 배열의 모든 값을 더합니다.
let sumFromThree: Int = numbers.reduce(3) {
    print("\($0) + \($1)")
    // 3 + 1
    // 4 + 2
    // 6 + 3
    return $0 + $1
}
print(sumFromThree) // 9
print()

// 초깃값이 3이고 정수 배열의 모든 값을 뺍니다.
let subtractFromThree: Int = numbers.reduce(3) {
    print("\($0) - \($1)")
    // 3 - 1
    // 2 - 2
    // 0 - 3
    return $0 - $1
}
print(subtractFromThree) // -3
print()

// 문자열 배열을 reduce(_:_:) 메서드를 통해 연결시킵니다.
let names: [String] = ["Chope", "Jay", "Joker", "Nova"]

let reducedNames: String = names.reduce("yagom's friend : ") {
    return $0 + ", " + $1
}
print(reducedNames) // yagom's friend : , Chope, Jay, Joker, Nova
```
다음과 같이 map 메서드와 filter 메서드, reduce 메서드를 같이 사용할 수도 있습니다.

```swift
let numbers: [Int] = [1, 2, 3, 4, 5, 6, 7]

// 짝수를 걸러내어 각 값에 3을 곱해준 후 모든 값을 더합니다.
var result: Int = numbers.filter { $0 % 2 == 0 }
                            .map { $0 * 3 }
                            .reduce(0) { $0 + $1 }
print(result) // 36

// for-in 구문 사용 시
result = 0

for number in numbers {
    guard number % 2 == 0 else {
        continue
    }
    
    result += number * 3
}

print(result) // 36
```


### 15.4 맵, 필터, 리듀스의 활용

목록의 친구들을 특정 조건으로 분류하여 콘솔에 출력하는 예제로 맵, 필터, 리듀스를 통합해서 사용해보겠습니다. 먼저 친구들의 정보를 담을 수 있는 구조체 Friend와 성별을 나타내는 열거형 Gender를 정의하고 친구들의 정보를 담아둘 배열 friends를 생성합니다.

```swift
enum Gender {
    case male, female, unknown
}

struct Friend {
    let name: String
    let gender: Gender
    let location: String
    var age: UInt
}

var friends: [Friend] = [Friend]()

friends.append(Friend(name: "Yoobato", gender: .male, location: "발리", age: 26))
friends.append(Friend(name: "JiSoo", gender: .male, location: "시드니", age: 24))
friends.append(Friend(name: "JuHyun", gender: .male, location: "경기", age: 30))
friends.append(Friend(name: "JiYoung", gender: .female, location: "서울", age: 22))
friends.append(Friend(name: "SungHo", gender: .male, location: "충북", age: 20))
friends.append(Friend(name: "JungKi", gender: .unknown, location: "대전", age: 29))
friends.append(Friend(name: "YoungMin", gender: .male, location: "경기", age: 24))
```
그런데 위 코드에 입력된 자료는 작년 자료입니다. 그래서 친구들의 나이는 실제 나이보다 한 살 더 적게 적혀있습니다. 일단, 이 점을 기본 전제로 조건에 맞는 친구를 찾을 예정입니다. 조건은 "서울 외에 지역에 거주하며 25세 이상인 친구" 입니다. 어떻게 찾아낼지 한 번 코딩해봅시다.









```swift
var result: [Friend] = friends.map { Friend(name: $0.name, gender: $0.gender, location: $0.location, age: $0.age + 1) }
result = result.filter { $0.location != "서울" }
                .filter { $0.age >= 25 }
let string: String = result.reduce("서울 외에 지역에 거주하며 25세 이상인 친구") { $0 + "\n" + "\($1.name) \($1.gender) \($1.location) \($1.age)세" }
print(string)
```
먼저 map으로 나이를 한 살씩 더해 새 Friend 배열을 생성해줍니다. 그리고 filter로 서울에 살고 있는 친구들과 25세 미만인 친구들을 걸러낸 후, reduce로 원하는 모양으로 합쳐서 출력할 수 있습니다.



## CHAPTER 16 모나드

모나드(Monad)는 특정한 상태로 값을 포장하는 것에서 출발합니다. 스위프트에서는 이를 옵셔널이라는 형태로 구현했는데, 값이 있을지 없을지 모르는 상태 속에 포장하는 것입니다.

함수객체와 모나드는 특정 기능이 아닌 디자인 패턴 혹은 자료구조라고 할 수 있습니다. 모나드를 이해하기에 앞서 이해해야 할 몇 가지 개념이 있습니다. 옵셔널을 하나하나 파헤쳐 보면서 순서에 따라 조금씩 알아보겠습니다.

### 16.1 컨텍스트

컨텍스트(Context)는 '콘텐츠를 담고있는 그 무엇인가'를 뜻합니다. 즉, 물컵에 물이 담겨있으면 물은 콘텐츠고 물컵은 컨텍스트라고 볼 수 있습니다.

컨텍스트에 대해 알아보기 전에 옵셔널을 다시 한 번 되새겨볼 필요가 있습니다. 옵셔널은 열거형으로 구현되어 있어서 열거형 케이스의 연관 값(p.100)을 통해 인스턴스 안에 연관 값을 가지는 형태입니다. 옵셔널이 값을 가지고 있지 않다면 열거형의 .none 케이스로, 값을 가지고 있다면 열거형의 .some(value) 케이스로 값을 지니게 됩니다. 옵셔널의 값을 추출한다는 것은 열거형 인스턴스 내부의 .some(value) 케이스의 연관 값을 꺼내오는 것과 같습니다.

2라는 숫자를 옵셔널로 둘러싸면, 컨텍스트 안에 2라는 콘텐츠가 들어가는 모양새입니다.(p.278의 그림 16-1 참조) 그리고 '컨텍스트는 2라는 값을 가지고 있다'고 말할 수 있습니다. 만약 값이 없는 옵셔널 상태라면 '컨텍스트는 존재하지만 내부에 값이 없다'고 할 수 있습니다.

옵셔널은 some과 none이라는 두 가지의 컨텍스트를 가집니다. 다음은 Int 타입의 값을 전달받아 3을 더하여 반환하는 함수입니다.

```swift
func addThree(_ num: Int) -> Int {
    return num + 3
}
```
addThree(_:) 함수의 전달인자로 컨텍스트에 들어있지 않은 순수 값인 2를 전달하면 정상적으로 함수를 실행할 수 있습니다. addThree() 함수는 매개변수로 일반 Int 타입의 값을 받기 때문입니다. (p.279의 그림 16-2 참조)

```swift
addThree(2) // 5
```
그러나 다음처럼 옵셔널을 전달인자로 사용하려고 한다면 오류가 발생합니다. 순수한 값이 아닌 무언가(여기서 옵셔널의 some)로 둘러싸인 컨텍스트가 전달되었기 때문입니다. (.79의 그림 16-3 참조)

```swift
addThree(Optional(2)) // 오류 발생
```


### 16.2 함수객체

기존에 배웠던 맵 함수는 컨테이너의 값을 변형시킬 수 있는 고차함수였습니다. 그리고 옵셔널은 컨테이너(컨텍스트가 일종의 컨테이너 역할을 합니다)와 값을 가지기 때문에 맵 함수를 사용할 수 있습니다. 다음과 같이 맵 함수를 사용하면 컨테이너 안의 값을 처리할 수 있습니다.

```swift
Optional(2).map(addThree) // Optional(5)
```
따로 함수가 없어도 클로저로 대신해 사용할 수도 있습니다. (p.280의 그림 16-4 참조)

```swift
var value: Int? = 2

value.map { $0 + 3 } // Optional(5)

value = nil

value.map { $0 + 3 } // nil
```
함수객체란, 맵을 적용할 수 있는 컨테이너 타입이라고 말할 수 있습니다. 이는, 앞서 맵을 사용해보았던 Array, Dictionary, Set 등의 컬렉션 타입이 함수객체라는 것입니다.

맵을 사용하여 컨테이너 내부의 값을 처리할 수 있다는 것을 살펴보았는데, p.281의 그림 16-5를 참조하여 함수객체에서 맵이 어떻게 동작하는지 이해해보고 아래 코드를 살펴봅시다.

```swift

```
옵셔널의 map(_:) 메서드를 호출하면 옵셔널 스스로 값을 가지고 있는지 없는지 switch 구문으로 판단합니다. 값이 있다면 전달받은 함수에 자신의 값을 적용한 결괏값을 다시 컨텍스트에 넣어 반환하고, 그렇지 않다면 함수를 실행하고 빈 컨텍스트를 반환합니다. Optional(2).map(addThree) 코드를 실행할 때 어떤 일이 벌어지는지 p.282의 그림 16-6, 그림 16-7을 참조하여 알아봅시다.

### 16.3 모나드

모나드(Monad)는 함수객체의 일종입니다. 함수객체는 맵 함수를 적용할 수 있는, 즉 맵 함수를 지원하는 컨테이너 타입이었습니다. 모나드는 거기에 더 나아가 값이 있을지 없을지 모르는 상태를 추가합니다. 즉, 모나드는 값이 있을 수도 있고 없을 수도 있는 컨텍스트를 가지는 함수객체 타입입니다.

함수객체는 포장된 값에 함수를 적용할 수 있었습니다. 그래서 모나드도 컨텍스트에 포장된 값을 처리하여 컨텍스트에 포장된 값을 다시 반환하는 함수를 적용할 수 있었습니다. 그래서 스위프트에서는 포장된 값을 받아서 값이 있으면 포장이 풀어서 값을 처리한 후 포장된 값을 반환하고, 값이 없으면 값이 없는대로 다시 포장하여 반환하는 플랫맵(Flatmap) 이라는 메서드를 지원합니다.

맵과 같이 플랫맵도 함수를 매개변수로 받고, 옵셔널은 모나드이므로 플랫맵을 사용할 수 있습니다. 다음과 같이 짝수면 2를 곱해서 반환하고 짝수가 아니라면 nil을 반환하는 함수가 있을 때, Optional(3)의 플랫맵 및 Optional.none의 플랫맵에 이 함수를 전달했을 경우를 살펴보고 p.283의 그림 16-8, p.284의 그림 16-9를 참조하여 이해해봅시다.

```swift
func doubledEven(_ num: Int) -> Int? {
    if num % 2 == 0 {
        return num * 2
    }
    return nil
}

Optional(3).flatMap(doubledEven) // nil
Optional.none.flatMap(doubledEven) // nil
```
플랫맵과 맵과의 차이점이라면 내부의 값을 알아서 더 추출해준다는 것입니다. 플랫맵은 내부에 포장된 값도 추출하는게 가능합니다. 다음은 map 구문과 flatMap 구문의 비교입니다.

```swift
let optionalArr: [Int?] = [1, 2, nil, 5]

let mappedArr: [Int?] = optionalArr.map { $0 }
let flatmappedArr: [Int] = optionalArr.flatMap { $0 }

print(mappedArr) // [Optional(1), Optional(2), nil, Optional(5)]
print(flatmappedArr) // [1, 2, 5]
```
위 optionalArr는 p.284의 그림 16-10 처럼 이중 컨테이너의 형태를 띄고 있습니다. optionalArr는 Array라는 컨테이너의 내부에 Optional이라는 형태의 컨테이너들이 여러개 들어가 있는 형태입니다. 이 배열의 맵 메서드와 플랫맵 메서드를 각각 호출해 본다면 다른 결과를 볼 수 있습니다. 맵 메서드는 Array 내부에 값이 있으면 그 값을 그저 클로저의 코드에만 실행하고 결과를 다시 Array 컨테이너에 담는 반면, 플랫맵 메서드는 클로저를 실행하면 알아서 내부 컨테이너까지 값을 추출합니다. 따라서 mappedArr은 다시 [Int?] 타입이 되며, flatmmapedArr는 [Int] 타입이 됩니다.

다음은 삼중 컨테이너에 대해 중첩된 맵과 플랫맵을 사용해보았습니다.

```swift
let multipleContainer = [[1, 2, Optional.none], [3, Optional.none], [4, 5, Optional.none]]

let mappedMultipleContainer = multipleContainer.map { $0.map { $0 } }
let flatmmapedMultipleContainer = multipleContainer.flatMap { $0.flatMap { $0 } }

print(mappedMultipleContainer) // [[Optional(1), Optional(2), nil], [Optional(3), nil], [Optional(4), Optional(5), nil]]
print(flatmmapedMultipleContainer) // [1, 2, 3, 4, 5]
```
삼중 컨테이너(p.285의 그림 16-11 참조)의 모습에서도 볼 수 있듯이 map은 Array 컨테이너에 결괏값을 다시 담지만, flatMap은 값을 추출해 nil이 아닌 값을 Array 컨테이너에 담습니다.

이처럼 스위프트에서 옵셔널에 관련된 여러 컨테이너의 값을 연달아 처리할 때, 바인딩을 통해 체인 형식으로 사용할 수 있기에 맵보다는 플랫맵이 더욱 유용하게 쓰일 수 있습니다. 다음과 같이 Int 타입을 String 타입으로, 그리고 String 타입을 Int 타입으로 변환하는 과정을 체인 형식으로 구현할 수 있습니다.

```swift
func stringToInt(str: String) -> Int? {
    return Int(str)
}

func intToString(integer: Int) -> String? {
    return "\(integer)"
}

var optionalString: String? = "2"

var result: Any = optionalString.flatMap(stringToInt).flatMap(intToString).flatMap(stringToInt)
print(result) // Optional(2)

// result = optionalString.map(stringToInt)?.flatMap(stringToInt) // 더 이상 체인 연결 불가
result = optionalString.map(stringToInt) // 더 이상 체인 연결 불가
print(result) // Optional(Optional(2))
```
위 코드에서 String 타입을 Int 타입으로 변환하는 것은 실패할 가능성을 내포하기 때문에 결괏값을 옵셔널 타입으로 반환합니다. 플랫맵을 사용하여 체인을 연결하였을 때 결과는 옵셔널 타입입니다. 그러나 맵을 사용하여 체인을 연결하면 옵셔널의 옵셔널 형태로 반환됩니다. 그 이유는 플랫맵은 함수의 결괏값이 값이 있다면 추출해서 평평하게 만드는 과정을 내포하고, 맵은 그렇지 않기 때문입니다. 맵은 옵셔널 타입의 값을 옵셔널이라는 컨테이너 안에 다시 집어넣어 반환하고, 플랫맵은 추출 작업을 통해 옵셔널에서 꺼내온 값을 다시 옵셔널에 넣어주기 때문에 이같은 연쇄 연산도 가능한 것입니다.