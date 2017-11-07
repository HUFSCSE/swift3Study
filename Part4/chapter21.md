# Chapter 21 익스텐션

## 21.1 익스텐션이란
- 모든 타입에 새로운 기능을 추가할수 있는 기능.
- 모든타입 = (클래스, 구조체, 프로토콜, 열거형, 제너릭)

----
### 익스텐션으로 가능한것
- 연산 타입 프로퍼티, 연산 인스턴스 프로퍼티
- 타입 메서드, 인스턴스 메서드
- 이니셜라이져
- 서브스크립트
- 중첩타입
- 특정 프로토콜을 준수 할 수 있도록 기능 추가
----

# 익스텐션은 새로운기능을 추가할수는 있지만 기존에 것을 변경할수는 없다.
- 그림 21-1

|               | 상속 | 익스텐션  |
| ------------- |:-------------| :-----|
| 확장 | 수직 확장 | 수평 확장 |
| 사용      | 클래스 타입에서만 사용      |   클래스, 구조체, 프로토콜, 제네릭 등 모든 타입에서 사용 |
| 재정의      | 재정의 가능      |   재정의 불가 |

                                         
## 21.2 익스텐션 문법

~~~swift
extension 확장할타입이름 {
    //타입에 추가될 새로운 기능 구현
}

extension 확장할타입이름: protocol1, protocol2, protocol3,... {
    //타입에 추가될 새로운 기능 구현
}
~~~

- Double타입 살펴보자. 코드21-2

- 기본 Inittiallizer외에 대부분 기능들이 Extension 으로 구현되어있다.

- 다른 타입도 한번 살펴보자.

## 21.3 익스텐션으로 확장할 수 있는 항목
### 21.3.1 연산 프로퍼티
- 저장 프로퍼티 X
- 기존 프로퍼티에 프로퍼티 감시자 X
- 인스턴스 연산 프로퍼티 O
- 타입 연산 프로퍼티 O

~~~swift
extension Int{
    var isEve: Bool {
        return self % 2 == 0
    }
}
~~~
### 21.3.2 메서드
~~~swift
extension Int{
    //인스턴스 메서드
    func multiply(n: Int) -> Int{
        return self * n
    }
    //가변 메서드
    mutating func multiplySelf(n: Int){
        self = self * n
    }
    //타입 메서드
    static func isIntTypeInstance(_ instance: Any) -> Bool{
        return instance is Int
    }
}

var integer = 100
print(integer.multiply(n: 10))      //1000
integer.multiplySelf(n: 100)
print(integer)                      ///10000
let str: String = ""
print(Int.isIntTypeInstance(integer))//true
print(Int.isIntTypeInstance(str))    //false
~~~

### 21.3.3 이니셜라이저
- convenience Init 추가 가능하다.
- Init, deInit은 반드시 타입 구현부에 위치해야한다.

~~~swift
//그러나, 값타입의 경우 관계없다.
struct Apple{
    var color: String
}
extension Apple{
    init() {
        self.color = ""
    }
}

let a = Apple()
~~~


~~~~
- p.374 

값 타입의 익스텐션을 통한 추구한 이니셜라이져에서 그 타입의 기본 이니셜라이져와, 멤버 이니셜라이져를 호출할 수있는 조건

- 모든 저장 프로퍼티가 기본값을 가지고 있습니다.
- 타입이 기본 이니셜라이저와 멤버와이즈 이니셜라이저 외에 추가적인 사용자정의 이니셜라이저를 가지고 있지 않습니다.
~~~~

- 익스텐션을 통해 추가한 이니셜라이져 역시 초기화 의무를 갖는다.

~~~swift
//extension을 통한 Init예제
struct Size {
    var width: Double = 0.0
    var height: Double = 0.0
}

struct Point {
    var x: Double = 0.0
    var y: Double = 0.0
}

struct Rect {
    var origin: Point = Point()
    var size: Size = Size()
}

let defalutRect = Rect()
let memberwize = Rect(origin: Point(), size: Size(width: 12.0, height: 144.2))

extension Rect{
    init(center: Point, size: Size) {
        let originX = center.x - (size.width / 2)
        let originY = center.y - (size.height / 2)
        self.init(origin: Point(x: originX, y: originY), size: size)
    }
}

let centerRect = Rect(center: Point(x: 1.0, y: 2.0), size: Size(width: 12.0, height: 144.0))
~~~


### 21.3.4 서브스크립트

~~~swift
extension String{
    subscript(appendValue: String) -> String{
        return self + appendValue
    }
}

print("abc"["dfg"])
~~~

### 21.3.5 중첩 데이터 타입
- 타입안에 또다른 타입이 선언되었을때
~~~swift
//Int타입안에 열거형 Kind가 선언되었다.
extension Int{
    enum Kind{
        case negative, zero, positive
    }
    var kind: Kind{
        switch self {
        case 0:
            return .zero
        case let x where x > 0:
            return .positive
        default:
            return .negative
        }
    }
}
print(1.kind)                           //positive
print(0.kind)                           //zero
print((-1).kind)                        //nagative

func printIntegerKinds(numbers: [Int]){
    for number in numbers{
        switch number.kind{
        case .negative:
            print("- ", terminator: "")
        case .zero:
            print("0 ", terminator: "")
        case .positive:
            print("+ ", terminator: "")
        }
    }
    print("")
}

printIntegerKinds(numbers: [1,-2,0,-4]) //+ - 0 -
~~~