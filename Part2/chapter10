# chapter 10. 프로퍼티와 메서드

* 프로퍼티는 클래스, 구조체, 열거형 등에 관련된 값들
* 메서드는 특정 타입에 관련된 함수

## 10.1 프로퍼티

* 크게 저장프로퍼티, 연산프로퍼티, 타입프로퍼티로 나눌 수 있다.
* 저장프로퍼티 : 변수 또는 상수
* 연산프로퍼티 : 값을 저장한 것이 아니라 특정 연산을 수행한 결과값
* 타입프로퍼티 : 틀정 타입에 사용되는 프로퍼티
* 프로퍼티갑시자 : 프로퍼티의 값이 변할 때 특정 액션을 수행

### 10.1.1 저장 프로퍼티

#### 저장프로퍼티의 선언 및 인스턴스 생성

```swift
struct CoordinatePoint{
    var x: Int // 저장프로퍼티
    var y: Int // 저장프로퍼티
}

let heechanPoint: CoordinatePoint = CoordinatePoint(x: 10, y: 5)

class Position{
    var point: CoordinatePoint
    
    let name: String // 저장프로퍼티(상수)
    
    init(name: String, currentPoint: CoordinatePoint){
        self.name = name
        self.point = currentPoint
    }
}

let heechanPosition: Position = Position(name: "heechan", currentPoint: heechanPoint)
```

#### 저장 프로퍼티의 초깃값 지정

```swift
struct CoordinatePoint{
    var x: Int = 0 // 저장프로퍼티
    var y: Int = 0 // 저장프로퍼티
}

let heechanPoint: CoordinatePoint = CoordinatePoint()
let wizplanPoint: CoordinatePoint = CoordinatePoint(x: 10, y: 5) // 가능

print("heechan's point : \(heechanPoint.x)")
print("wizplanPoint point : \(wizplanPoint.x)")

class Position{
    var point: CoordinatePoint =  CoordinatePoint()
    
    var name: String = "Uknown" // 저장프로퍼티
    
}

let heechanPosition: Position = Position()
heechanPosition.point = heechanPoint
heechanPosition.name = "heechan"

```

#### 옵셔널 저장 프로퍼티

```swift
struct CoordinatePoint{
    var x: Int
    var y: Int
}

class Position{
    var point: CoordinatePoint? //옵셔널
    
    let name: String
    
    init(name: String){
        selft.name = name
    }
    
}
//이름은 필수지만 위치는 모를수도 있당
let heechanPosition: Position = Position(name: "heechan")
//위치를 알게되면 그 때 위치 값을 할당
heechanPosition.point = CoordinatePoint(x: 20, y: 10)
```

### 10.1.2 지연 저장 프로퍼티

* 호출이 있어야값을 초기화한다.
* lazy 키워드 사용
* 복잡한 클래스나 구조체를 구현할 때 많이 사용

```
클래스 인스턴스의 저장 프로퍼티로 다른 클래스 인스턴스나 구조체 인스턴스가 할당되어야 할 때가 있다.
이때 인스턴스를 초기화 하면서 저장프로퍼티로 쓰이는 인스턴스들이 한 번에 생성되어야할때, 굳이 모든 저장 프로퍼티를 사용할 필요가 없을때 사용한다.
```

```swift
struct CoordinatePoint{
    var x: Int = 0
    var y: Int = 0
}

class Position{
    lazy var point: CoordinatePoint = CoordinatePoint() //옵셔널
    
    let name: String
    
    init(name: String){
        self.name = name
    }
    
}
//이름은 필수지만 위치는 모를수도 있당
let heechanPosition: Position = Position(name: "heechan")

//여기서 CoordinatePoint 생성
print(heechanPosition.point)
```

### 10.1.3 연산 프로퍼티

#### 메서드로 구현된 접근자와 설정자
```swift
struct CoordinatePoint{
    var x: Int
    var y: Int
    
    //접근자
    func oppositePoint() -> CoordinatePoint{
        return CoordinatePoint(x: -x, y: -y)
    }
    
    mutating func setOppositePoint(_ opposite: CoordinatePoint){
        x = -opposite.x
        y = -opposite.y
    }
    
}


var heechanPosition: CoordinatePoint = CoordinatePoint(x: 10, y: 20)

//현재좌표
print(heechanPosition) // 10, 20

//대칭좌표
print(heechanPosition.oppositePoint()) // -10, -20

//대칭 좌표를 (15, 10)으로 설정하면
heechanPosition.setOppositePoint(CoordinatePoint(x: 15, y:10))
//현재 좌표는 -15, -10으로 됨
print(heechanPosition)
```

#### 연산프로퍼티의 정의와 사용
```swift
struct CoordinatePoint{
    var x: Int
    var y: Int
    
    //접근자
    var oppositePoint: CoordinatePoint{
    
        get{
            return CoordinatePoint(x: -x, y: -y)
        }
    
        set(opposite){
            x = -opposite.x
            y = -opposite.y
        }
    }
    
}


var heechanPosition: CoordinatePoint = CoordinatePoint(x: 10, y: 20)

//현재좌표
print(heechanPosition) // 10, 20

//대칭좌표
print(heechanPosition.oppositePoint) // -10, -20

//대칭 좌표를 (15, 10)으로 설정하면
heechanPosition.oppositePoint = CoordinatePoint(x: 15, y:10)
//현재 좌표는 -15, -10으로 됨
print(heechanPosition)


```

#### 매개변수 이름을 생략한 설정자

```swift
struct CoordinatePoint{
    var x: Int
    var y: Int
    
    //접근자
    var oppositePoint: CoordinatePoint{
    
        get{
            return CoordinatePoint(x: -x, y: -y)
        }
    
        set{
            x = -newValue.x
            y = -newValue.y
        }
    }
    
}


var heechanPosition: CoordinatePoint = CoordinatePoint(x: 10, y: 20)

//현재좌표
print(heechanPosition) // 10, 20

//대칭좌표
print(heechanPosition.oppositePoint) // -10, -20

//대칭 좌표를 (15, 10)으로 설정하면
heechanPosition.oppositePoint = CoordinatePoint(x: 15, y:10)
//현재 좌표는 -15, -10으로 됨
print(heechanPosition)

```

### 10.1.4 프로퍼티 감시자

* 값이 변경됨에 따라 적절한 액션을 취할 수 있음
* 변경값이 같아도 호출됨
* 일방저장프로퍼티에만 적용가능 (지연x)
* 재정의를 통해 상속된 프로퍼티에도 적용가능
* willSet : 값이변경되기 전
* didSet : 변경된 직후

```swift
class Account{
    var credit: Int = 0{
        willSet{
            print("잔액이 \(credit)원에서 \(newValue)원으로 변경될 예정입니다.")
        }
        didSet{
            print("잔액이 \(oldValue)원에서 \(credit)원으로 변경되었습니다.")
        }
    }
}

let myAccount:Account = Account()

myAccount.credit = 1000
```

### 10.1.5 전역변수와 지역변수
### 10.1.6 타입 프로퍼티
```swift
class AClass{
    //저장 타입 프로퍼티
    static var typeProperty: Int = 0
    
    //저장 인스턴스 프로퍼티
    var instanceProperty: Int = 0{
        didSet{
            AClass.typeProperty = instanceProperty + 100
        }
    }
    
    static var typeComputedProperty: Int{
        get{
            return typeProperty
        }
        
        set{
            typeProperty = newValue
        }
    }
}

AClass.typeProperty = 123

let classInstance: AClass = AClass()

classInstance.instanceProperty = 100

print(AClass.typeProperty) // 200
print(AClass.typeComputedProperty) // 200
```
