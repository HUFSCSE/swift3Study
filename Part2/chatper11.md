# Chapter 11 인스턴스 생성 및 소멸

## 11.1 인스턴스 생성
**이니셜라이저**를 정의하면 초기화과정을 직접 구현 가능
스위프트의 이니셜라이저는 반환값이 없다.
init 키워드를 사용해서 이니셜라이저 메소드임을 표현

```swift
class SomeClass{
    init(){
        //초기화 시 필요한 코드
    }
}

struct SomeClass{
    init(){
        //초기화 시 필요한 코드
    }
}

enum SomeClass{
    case someCase
    
    init(){
        //열겨형은 초기화 시 반드시 케이스중 하나가 되야됨
        self = .someCase
    }
}
```

```
초기화될때 프로퍼티 감시자 메서드가 호출되지않는다.
```

```
struct Area{
    var squareMeter: Double
    
    init(fromPy py: Double){
        squareMeter = py * 3.3058
    }
    
    init(fromSquareMeter squareMeter: Double){
        self.squareMeter = squareMeter
    }
    
    init(value: Double){
        squareMeter = value
    }
    
    init(_ value: Double){
        squareMeter = value
    }
}

let roomOne: Area = Area(fromPy: 15.0)
print(roomOne.squareMeter)

let roomTwo: Area = Area(fromSquareMeter: 33.06)
print(roomTwo.squareMeter)

let roomThree: Area = Area(value: 30.0)
let roomFour: Area = Area(55.0)
```

### 11.1.3 옵셔널 프로퍼티타입
* 초기화 값을 지정해주기 어려운 경우 옵셔널로 선언가능. 값을 할당해주지 않으면 nil 반환

### 11.1.4 상수 프로퍼티
* 상수로 선언되어있는 값은 초기화하는 과정이서만 값을 할당할 수 있고 이후 변경불가

```swift
class person{
    let name: String
    var age: Int?
    
    init(name: String){
        self.name = name
    }
}

let heechan: Person = Person(name:"heechan")
heechan.name = "Eric" // 에러
```
### 11.1.5 기본 이니셜라이저와 멤버와이즈 이니셜라이저
* 구조체는 이니셜라이저를 구현하지 않아도 멤버와이즈 이니셜라이저를 기본으로 제공한다.

### 11.1.6 초기화 위임
```swift
enum Student{
    case elementary, middle, high
    case none
    
    init(){
        self = .none
    }
    
    init(koreanAge: Int){
        switch koreanAge{
        case 8...13:
            self = .elementary
        case 14...16:
            self = .middle
        case 17...19:
            self = .high
        default:
            self = .none
        }
    }
    
    init(bornAt: Int, currentYear: Int){
        self.init(koreanAge: currentYear - bornAt + 1)
    }
}

var younger: Student = Student(koreanAge: 16)
print(younger)

younger = Student(bornAt: 1998, currentYear:2016)
print(younger)
```
### 11.1.7 실패 가능한 이니셜라이저
이니셜라이저를 통해 인스턴스가 초기화되지 못하는 예외상황.

**실패 가능한 이니셜라이저** 라고 한다.

```swift
class Person{
    let name: String
    var age: Int?
    
    init?(name: String){
        if name.isEmpty{
            return nil
        }
        self.name = name
    }
    
    init?(name: String, age: Int){
        if name.isEmpty || age < 0 {
            return nil
        }
        self.name = name
        self.age = age
    }
}

let heechan: Person? = Person(name: "heechan", age: 99)
if let person: Person = heechan{
    print(person.name)
}else{
    print("Person wasn't initialized")
}

let chope: Person? = Person(name: "chope", age: -10)
if let person: Person = chope{
    print(person.name)
}else{
    print("Person wasn't initialized")
}

```

### 11.1.8 함수를 사용한 프로퍼티 기본값 세팅
* 사용자 정의 연산을통해 저장 프로퍼티 기본값을 설정하고자 한다면 클로저나 함수를 사용하여 프로퍼티 기본값을 제공할 수 있다.
* 인스턴스가 초기화될 때 함수나 클로저가 호출되면서 연산을 통한 결괏값을 포르퍼티 기본값으로 제공
* -> 클로저나 함수의 반환 타입은 프로퍼티 타입과 일치해야한다.
* 클로저가 실행되는 시점은 초기화될 때 인스턴스의 다른 프로퍼티의 값이 세팅되기 전이라는 것을 명심
* 클로저 내부에서는 인스턴스의 다른 프로퍼티를 사용하여 연산할 수 없다.
* 다른 포로퍼티가 기본값을 가지고 있다고 해도 안됨
* 클로저 내부에서는 self프로퍼티도 사용불가
* 인스턴스 메서드를 호출 할 수도없다.

```swift
struct Student{
    var name: String?
    var number: Int?
}

class SchoolClass{
    var students: [Student] = {
        var arr: [Student] = [Student]()
        
        for num in 1...15{
            var student: Student = Student(name: nil, number: num)
            arr.append(student)
        }
        
        return arr
        
    }()
}

//인스턴스가 생성되자마자 students프로퍼티에 15명의 학생을 가지고 있는 상태이다
let myClass: SchoolClass = SchoolClass()
print(myClass.students.count)
```

## 11.2 인스턴스 소멸
저번시간에했음.
