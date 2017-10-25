# Part 3 함수형 프로그래밍과 스위프트
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
