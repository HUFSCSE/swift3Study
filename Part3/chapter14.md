# Part 3 함수형 프로그래밍과 스위프트
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
