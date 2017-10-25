# Part 3 함수형 프로그래밍과 스위프트
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
